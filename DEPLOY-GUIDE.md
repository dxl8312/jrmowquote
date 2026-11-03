# jrmowquote.com 部署指南（Cloudflare Pages + Email Routing）

适用：纯静态站点（HTML/CSS/JS，无构建步骤）。目标：把本地 `lawn-mowing-calculator-prototype` 目录部署到 Cloudflare Pages，绑定域名 `jrmowquote.com`，并用 Cloudflare Email Routing 把 `hello@jrmowquote.com` 转发到你的个人 Gmail。

> 邮件方案已定：**CF Email Routing（免费）**。只收信转发，回信用个人 Gmail。若将来想回信也显示 `hello@`，再单独升级（Zoho Mail Lite 或免费 SMTP 服务），不要和 Email Routing 同时开（MX 记录会冲突）。

---

## 第 0 步：准备本地代码（重要）
确保 **仓库根目录直接包含 `index.html`**，不要多套一层文件夹。

- ✅ 正确：`仓库根/index.html`、`仓库根/images/`、`仓库根/pages/`、`仓库根/sitemap.xml`、`仓库根/robots.txt`
- ❌ 错误：`仓库根/lawn-mowing-calculator-prototype/index.html`（多了子层，CF 会找不到首页）

本机项目目录（即 GitHub 仓库根）下的内容就是要上传的全部。建议把这个目录内容作为 GitHub 仓库根。

---

## 第 1 步：把代码推到 GitHub
入口：https://github.com （需注册/登录）

1. 右上角 **＋ → New repository**，仓库名随意（如 `jrmowquote`），**Public**，**不要**勾选 Add README（保留空仓库）。
2. 本地 Git Bash 执行（把目录内容推上去）：
   ```bash
   cd "<项目目录>"
   git init
   git add -A
   git commit -m "initial site"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/jrmowquote.git
   git push -u origin main
   ```
3. 刷新 GitHub 页面，确认 `index.html` 已在仓库根。

---

## 第 2 步：Cloudflare Pages 部署（连接 GitHub 自动部署）
入口：https://dash.cloudflare.com/login → 登录后左侧 **Workers 和 Pages**（或打开 https://dash.cloudflare.com/?to=/:account/workers-and-pages ）

1. 点 **Create → Pages → Connect to Git**。
2. 首次会请求授权 GitHub，点 **Authorize Cloudflare**。
3. 选第 1 步建的仓库 `jrmowquote`。
4. 构建设置（关键，别填错）：
   - **Framework preset**：选 **None**（无框架）
   - **Build command**：**留空**
   - **Build output directory**：**留空**（用仓库根；若向导不接受空，填 `.` 表示当前目录）
5. 点 **Save and Deploy**。
6. 等待约 1 分钟，出现 `https://jrmowquote.pages.dev`（名称可能带随机前缀）即部署成功。每次 `git push` 会自动重新部署。

---

## 第 3 步：把域名 DNS 交给 Cloudflare（Spaceship 改 nameserver）
> Email Routing 和自定义域名都要求域名 DNS 由 Cloudflare 管理，所以先做这步。

入口（Cloudflare）：左侧 **Websites → Add Site**（或 https://dash.cloudflare.com/?to=/:account/websites ）

1. 输入 `jrmowquote.com` → 选 **Free** 计划 → 继续。
2. CF 会扫描现有 DNS 记录（基本为空，正常），直接 **Continue**。
3. CF 给出 **2 个 nameserver**，形如：
   ```
   xxx.ns.cloudflare.com
   yyy.ns.cloudflare.com
   ```
4. 打开 **Spaceship 域名管理**（你买域名的地方），找到 `jrmowquote.com` 的 **Nameservers / DNS** 设置，把原来的改成上面这两个 Cloudflare nameserver，保存。
5. 回到 CF 点 **Done, check nameservers**。通常几分钟～48 小时生效（多数很快）。CF 后台该站点出现 "Active" 即成功。

---

## 第 4 步：绑定自定义域名（拿免费 SSL）
入口：CF 后台 → **Workers 和 Pages → 你的项目 → Custom domains**

1. 点 **Set up a custom domain**，输入 `jrmowquote.com`，按提示确认。
2. CF 自动添加相应 DNS 记录并签发 **免费 SSL 证书**（几分钟）。
3. 生效后访问 `https://jrmowquote.com` 即是正式站点。
4. （可选）在 Pages 设置里把 `*.pages.dev` 设为重定向到正式域，避免重复索引。

---

## 第 5 步：启用 Email Routing（hello@ → Gmail）
入口：CF 后台 → **你的域名 jrmowquote.com → Email → Email Routing**

1. 点 **Enable Email Routing**，按向导添加一条路由规则：
   - **Custom address**：`hello`
   - **Destination**（目标）：填你的个人 Gmail，如 `you@gmail.com`
2. CF 会给 Gmail 发一封验证邮件，去 Gmail 点确认。
3. （可选）再加一条 **Catch-all**（收所有 `@jrmowquote.com`）→ 转到 Gmail，防止漏信。
4. 验证：用另一个邮箱给 `hello@jrmowquote.com` 发信，应几秒内进你 Gmail 收件箱。

> 注意：Email Routing 只负责**收信转发**。你回信时发件人默认是你的 Gmail 地址——这是免费方案的固有行为，访客通常不介意。

---

## 第 6 步：提交 Google Search Console（收录）
入口：https://search.google.com/search-console

1. 添加资源 → **网址前缀** 输入 `https://jrmowquote.com/`。
2. 验证方式选 **HTML 标记** 或 **Google Analytics**；最简单是用 Cloudflare 的 GSC 验证（CF 后台域名 → SEO → 可一键验证），或下载验证文件放到站点根后重新部署。
3. 验证通过后，左侧 **Sitemaps → 提交 `https://jrmowquote.com/sitemap.xml`**。
4. 后续每次加新页面，更新 `sitemap.xml` 的 `lastmod` 并重新部署即可。

---

## 第 7 步（稍后）：申请 AdSense
- 等站点内容稳定、页面更完整（建议补完那 7 个页面）、且已用正式域名运行一段时间后再申。
- 入口：https://www.google.com/adsense/ ，填真实域名 `https://jrmowquote.com`。
- 拿到 `ca-pub-` ID 后，把 `ads.txt` 里的 `pub-0000000000000000` 换成真实 ID 并重新部署。

---

## 部署后自检清单
- [ ] `https://jrmowquote.pages.dev` 能打开，首页计算器可正常算价
- [ ] 本地 `git push` 后，pages.dev / 正式域自动更新
- [ ] `https://jrmowquote.com` 可访问、地址栏有锁（SSL 正常）
- [ ] 给 `hello@jrmowquote.com` 发测试信，Gmail 能收到
- [ ] GSC 已验证域名并提交 sitemap
- [ ] 全站无 `localhost` / `127.0.0.1` 硬编码（已确认无）
- [ ] `your-domain.com` 占位已全部替换为 `jrmowquote.com`（已确认 0 残留）

---

## 常见坑
- **首页 404**：几乎都是"仓库根没有 index.html"（多了子层）。回到第 0 步。
- **改 URL 不重定向会掉排名**：现有 8 页文件名/目录一个都别动；新增页面是安全的。
- **Email Routing 与 Zoho 同开会冲突**：二选一，本方案只用 CF Email Routing。
- **不要现在就把 pages.dev 提交 GSC/外发**：先用正式域 `jrmowquote.com` 对外，避免 Google 索引错域名。
