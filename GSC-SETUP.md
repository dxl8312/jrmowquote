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

## 第 2 步：复制验证 TXT 记录

GSC 会显示一段类似：

```
google-site-verification=abcd1234EFGH5678ijkl
```

全选复制（**含 `google-site-verification=` 前缀**）。

---

## 第 3 步：到 Cloudflare 加这条 TXT 记录

1. 打开 https://dash.cloudflare.com → 左侧 **Domains** → 点 **jrmowquote.com**
2. 进入该域 Dashboard → 左侧 **DNS** → **Records**
3. 点 **Add record / 添加记录**，填写：
   - **Type / 类型**：`TXT`
   - **Name / 名称**：`@`（或留空，表示根域）
   - **Content / 内容**：粘贴刚才复制的 `google-site-verification=...` 整段
   - **TTL**：`Auto`（自动）
   - **Proxy status / 代理状态**：**DNS only（灰云）** —— TXT 验证记录不要开代理
4. 点 **Save / 保存**

---

## 第 4 步：回 GSC 点验证

- 回到 GSC 验证窗口 → 点 **验证 / Verify**
- 通常几秒到几分钟通过（DNS 已在 CF，传播快）
- 通过后会显示「所有权已验证 / Ownership verified」

> 若提示失败：等 1–2 分钟再点一次（TXT 记录全球同步需一点时间）。不要重复添加多条 TXT。

---

## 第 5 步：提交 sitemap

1. GSC 左侧菜单 → **Sitemaps / 站点地图**
2. 在「Add a new sitemap / 添加新的站点地图」输入框填：`sitemap.xml`（只填文件名，不用填完整 URL）
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
