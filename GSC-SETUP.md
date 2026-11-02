# JrMowQuote — Google Search Console 验证与 sitemap 提交步骤

> 适用前提（已确认通过）：
> - `sitemap.xml` 8 个 URL 全部指向 `https://jrmowquote.com/`（无 localhost / pages.dev / 旧域残留）
> - `robots.txt` 指向同 sitemap
> - 代码已 push 到 `dxl8312/jrmowquote`（commit `d6f4052`），线上 = 本地最新

---

## 第 1 步：打开 GSC 并添加资源

1. 访问 https://search.google.com/search-console
2. 登录你的 Google 账号（建议用常用 Gmail）
3. 点 **添加资源 / Add property**
4. 选 **网域 / Domain**（不是「网址前缀 URL prefix」）→ 输入 `jrmowquote.com`（不要带 `https://`，也不要带 `www`）→ 继续

> 为什么选「网域」：验证整个域，覆盖 http/https、带/不带 www 所有版本；只需在 DNS 加一条 TXT，不改代码、不重新部署。

---

## 第 2~4 步：验证所有权（自动优先，手动兜底）

> 实际最新流程：GSC 在「网域」验证时，经常只需点 **验证 / Verify** 并按提示**授权**（如关联 Cloudflare / Google 账号）即可**自动完成**，几秒通过，无需手动复制 TXT。
> 如果自动授权这条路不通（界面没出现授权按钮、或验证失败），再用下面的**手动 TXT 方式**兜底。

### 手动 TXT 方式（备选）

1. GSC 显示一段类似：
   ```
   google-site-verification=abcd1234EFGH5678ijkl
   ```
   全选复制（**含 `google-site-verification=` 前缀**）。
2. 打开 https://dash.cloudflare.com → **Domains** → **jrmowquote.com** → **DNS** → **Records** → **Add record**：
   - **Type**：`TXT`
   - **Name**：`@`（或留空，表示根域）
   - **Content**：粘贴上面整段
   - **TTL**：`Auto`
   - **Proxy status**：**DNS only（灰云）**
3. 回到 GSC 点 **验证 / Verify**。通常几秒到几分钟通过。失败就等 1–2 分钟再点一次，不要重复加多条 TXT。

---

## 第 5 步：提交 sitemap（⚠️ 网域型必须填完整 URL）

1. GSC 左侧菜单 → **Indexing / 编制索引 → Sitemaps / 站点地图**
2. 在「Add a new sitemap / 添加新的站点地图」输入框填写：

   - **网域（Domain）型资源（本教程第 1 步选的就是这种）→ 必须填完整绝对地址：**
     ```
     https://jrmowquote.com/sitemap.xml
     ```
   - 网址前缀（URL prefix）型资源 → 才填纯文件名 `sitemap.xml`

   > ⚠️ 报「站点地图地址无效 / Invalid sitemap address」= 网域型却填了纯文件名。
   > 网域型覆盖 http/https/www/非www 全部，GSC 不知根在哪，所以必须写全。

3. 点 **Submit / 提交**
4. 状态从「Pending / 待处理」变为「Success / 成功」，已发现 URL 数 = 8

---

## 第 6 步（可选但推荐）：URL 检查

- 左侧 **URL Inspection / 网址检查** → 输入 `https://jrmowquote.com/` → 查看是否「已编入索引」
- 新站首次提交后，Google 抓取需要 **几天到几周**，属正常，不用急

---

## 完成后的正常预期

- Google 开始抓取你的 8 个页面
- 几周后搜索 `lawn mowing cost calculator` 可能看到你的站（排名爬升需时间 + 内容厚度支撑）

## 注意事项

- GSC 验证 与 AdSense 申请互不冲突，可并行。
- sitemap 提交后，今后新增页面只需更新 `sitemap.xml` 并重新 push，Google 会自动发现（也可在 GSC 点「重新提交」）。
- 当前 sitemap 的 `lastmod` 是 2026-10-30；若之后大改内容，记得更新 `lastmod` 再 push。
- `ads.txt` 与 `index.html` 里的 `ca-pub-0000000000000000` 是 AdSense 占位（非 GSC 用），等申请下来再替换。
