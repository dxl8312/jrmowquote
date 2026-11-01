# 部署前全面自检报告 — jrmowquote.com

**日期**：2026-10-30（结合 10-28 / 10-29 / 10-30 备忘录）
**范围**：8 个 HTML 页面 + 20 张图片 + sitemap.xml / robots.txt / ads.txt + 部署配置
**方法**：静态脚本扫描（标签闭合、内链、图片引用、元标签、占位符、JS 语法）+ grep 人工复核

---

## ✅ 结论：已达到「可部署」状态

**阻断性问题 = 0**。所有页面结构完整、链接有效、图片齐全、导航一致、JS 语法通过，可随时推到 Cloudflare Pages。
脚本初扫报出的 5 个 FAIL / 8 个 WARN 经逐一复核，**均为「已知占位待填」或「扫描误报」**，无任何需要修复才能部署的真 bug。

---

## 一、检查项结果总览

| 检查项 | 结果 | 说明 |
|---|---|---|
| 文件结构（根含 index.html） | ✅ 通过 | CF Pages 必需条件满足 |
| HTML 标签闭合 | ✅ 通过 | 各页 `<tr>/<td>/<div>` 等经 grep 复核全部平衡 |
| 重复 id | ✅ 通过 | 0 个重复 id |
| 内部链接（href） | ✅ 通过 | 0 断链（相对路径全部命中） |
| 图片引用（src） | ✅ 通过 | 20 张图全部命中真实文件，0 缺 alt、0 缺宽高 |
| 导航一致性 | ✅ 通过 | 8 页 header.nav 签名统一（菜单 9 项 + 药丸按钮 + 品牌） |
| 元标签（canonical/og/twitter） | ✅ 通过 | 全部指向 `jrmowquote.com`，无 `your-domain.com` 残留 |
| index 内联 JS 语法 | ✅ 通过 | `node --check` 无报错 |
| sitemap.xml | ✅ 通过 | 8 条 URL，全部 `https://jrmowquote.com/`，XML 合法 |
| robots.txt | ✅ 通过 | `Allow: /` + 正确指向 sitemap |
| 硬编码 localhost/file:// | ✅ 通过 | 0 处，全相对路径 |
| 占位符 `REPLACE_ME_URL` | ✅ 通过 | 全站 0 处（第五轮已清理） |

---

## 二、初扫 FAIL / WARN 的复核结论

| 初判 | 实际结论 | 处置 |
|---|---|---|
| `index.html / diy-vs-hire.html` 含 `yourtag-20` | **已知占位**（Amazon Associates ID） | 注册后替换，不阻断部署 |
| `index.html / diy-vs-hire.html` 含 `pub-0000000000000000` | **Adsense 广告代码已预埋，用占位 client ID**（见第三节） | 注册后替换，不阻断部署 |
| `index.html <tr> open=28 close=29` | **误报**（grep 复核 29/29 平衡，扫描正则边界问题） | 无需处理 |
| `og.jpg / og-*.jpg` 报孤儿 | **误报**（通过 `og:image` meta 引用，非 `<img>`） | 无需处理 |
| `hero-suburban-lawn.webp` 报孤儿 | **误报**（被 preload + CSS 背景 + JSON-LD 引用） | 无需处理 |
| `ads.txt` 发布商 ID 为占位 | **已知**（同 ads 占位） | 注册后替换 |
| 移动端汉堡菜单未检测到 | **已知待办**（≤980px 导航整条隐藏，无切换按钮） | 建议上线后补（见待办清单） |
| 「广告单元代码已存在」 | **误报级**（实为正确预埋的占位广告代码） | 见第三节处置 |

---

## 三、⚠️ 一个需要你知晓的关键发现：广告代码已预埋

自检发现 `index.html`（第 21–26、667–670、751–754 行）和 `diy-vs-hire.html`（第 21 行）**已经插入了 AdSense 广告代码**，但用的是占位 ID：

- 加载脚本：`client=ca-pub-0000000000000000`
- 两个广告单元：`data-ad-slot="0000000000"` / `"0000000001"`

代码旁有注释说明这是「过审前空白、过审后替换」的预埋设计——**这种做法本身没问题，但有两个注意点**：

1. **不要现在就用占位 ID 提交 AdSense 审核**。AdSense 审核需要读到有效的发布商 ID。正确顺序是：拿到真实 `ca-pub-XXXXXXXXXXXXXXXXX` → 替换 loader 的 client → 在后台建 2 个广告单元拿到真实 slot 编号 → 替换 `data-ad-slot` → 再提交审核。
2. 替换范围（共 3 处文件）：`index.html`（loader + 2 个单元）、`diy-vs-hire.html`（loader）、`ads.txt`（写 `google.com, pub-XXXXXX, DIRECT, f08c47fec0942fa0`）。

> 注：此前的备忘录曾记为「广告代码尚未插入」，与现状不符——本次自检已纠正。

---

## 四、上线后 / 注册后待办清单（均非阻断）

| 优先级 | 事项 | 位置 | 备注 |
|---|---|---|---|
| 🔴 高 | 填 Amazon Associates ID | `index.html` + `diy-vs-hire.html` 共 5 处 `tag=yourtag-20` | 注册后替换；Amazon 要求 180 天内 ≥3 单否则关户 |
| 🔴 高 | 填 AdSense 真实 ID | `index.html` / `diy-vs-hire.html` 广告代码 + `ads.txt` | 拿到账户后替换，**再**提交审核 |
| 🟡 中 | 移动端汉堡菜单 | 全部 8 页 header | 当前 ≤980px 导航整条隐藏，窄屏丢失入口，建议补切换按钮 |
| 🟡 中 | 开通联系邮箱 | `hello@jrmowquote.com` | 经 Cloudflare Email Routing 转发到 Gmail（DNS 切到 CF 后做） |
| 🟢 低 | favicon.ico | 根目录 | 目前缺失，建议补，影响地址栏/书签体验 |
| 🟢 低 | 404.html | 根目录 | 目前缺失，建议补 |
| 🟢 低 | 补 7 个 SEO 页面 | Pricing / By Region / Best Time / Mulching / Aeration / Choose a Co / Seasonal | 内容厚度增强，非 AdSense/GSC 门槛；补后更新 sitemap |

---

## 五、部署到 Cloudflare Pages 后，建议手动验证

1. 首页计算器：输入 5000 sq ft 看是否出 $49；选 overgrown 看是否变 $94；清空看是否回 $49
2. 点击顶部 9 项导航，确认每页都能跳且不折行
3. 滚动检查图片是否全部加载（尤其 hero 背景图）
4. 浏览器缩到手机宽度（≤980px），确认导航状态（当前会整条消失，属已知项）
5. 查看页面源码确认 `canonical` 为 `https://jrmowquote.com/...`
6. 提交 GSC 后，用 URL 检查工具逐页验证「已编入索引」

---

**一句话**：代码可以放心部署。剩下全是「拿到 AdSense / Amazon 账号后填 ID」和「移动端体验增强」的运营类待办，没有会拦住上线的硬伤。
