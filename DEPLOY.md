# 部署到 GitHub Pages

把本文件夹发布为 GitHub Pages 站点，账号 **Bao-SH**，仓库名 **claude-explain-books**。

部署完成后的地址：

| 页面 | 地址 |
|---|---|
| 首页 | https://bao-sh.github.io/claude-explain-books/ |
| 人月神话 | https://bao-sh.github.io/claude-explain-books/mythical-man-month/ |
| 领域驱动设计 | https://bao-sh.github.io/claude-explain-books/domain-driven-design/ |

> GitHub Pages 的域名一律是小写，所以用户名 `Bao-SH` 在网址里写成 `bao-sh`。

---

## 一、首次部署

### 1. 登录 GitHub CLI

```bash
gh auth login
```

按提示依次选择：

- `GitHub.com`
- `HTTPS`
- `Login with a web browser`，在浏览器里用 **Bao-SH** 账号授权

确认登录成功：

```bash
gh auth status
```

输出里应当出现 `Logged in to github.com account Bao-SH`。

### 2. 初始化仓库并提交

```bash
cd ~/Documents/learn/claude-explain-books
git init -b main
git add .
git status        # 确认没有 .pdf / .epub 文件
git commit -m "Add book visualizations"
```

`.gitignore` 已经排除了 `*.pdf` 和 `*.epub`。提交前用 `git status` 再确认一次。

### 3. 在 GitHub 上创建仓库并推送

```bash
gh repo create Bao-SH/claude-explain-books --public --source=. --remote=origin --push
```

仓库必须是 **public**。免费账号的私有仓库不能开启 GitHub Pages。

### 4. 开启 GitHub Pages

**方式 A：命令行**

```bash
gh api -X POST repos/Bao-SH/claude-explain-books/pages \
  -f "source[branch]=main" -f "source[path]=/"
```

**方式 B：网页**

1. 打开 https://github.com/Bao-SH/claude-explain-books/settings/pages
2. **Source** 选 `Deploy from a branch`
3. **Branch** 选 `main`，目录选 `/ (root)`，点 **Save**

### 5. 等待发布并访问

查看发布状态：

```bash
gh api repos/Bao-SH/claude-explain-books/pages --jq '.status, .html_url'
```

状态变成 `built` 后，打开 https://bao-sh.github.io/claude-explain-books/ 。首次发布一般需要 1～2 分钟。

也可以在 https://github.com/Bao-SH/claude-explain-books/actions 查看 `pages build and deployment` 的运行情况。

---

## 二、日常更新

修改了某本书的页面之后：

```bash
cd ~/Documents/learn/claude-explain-books
git add .
git commit -m "Update DDD visualization"
git push
```

推送后约 1 分钟，网站会自动更新。浏览器如果还显示旧版本，按 `Cmd+Shift+R` 强制刷新。

## 三、添加一本新书

1. 新建子目录，例如 `clean-architecture/`，把页面放成 `clean-architecture/index.html`。页面必须是完整的 HTML 文档，包含 `<!doctype html>`、`<head>` 和 `<body>`。
2. 在根目录 `index.html` 的 `<div class="grid">` 里复制一张 `<a class="card">`，改好链接、书名、作者、简介和标签。`--c` 是卡片的主题色。
3. 在 `README.md` 的表格里加一行。
4. 按“日常更新”的步骤提交并推送。

## 四、直接跳到某一幕

在网址后面加 `#s<编号>`：

- https://bao-sh.github.io/claude-explain-books/mythical-man-month/#s3 （`#s0` – `#s7`）
- https://bao-sh.github.io/claude-explain-books/domain-driven-design/#s4 （`#s0` – `#s9`）

---

## 常见问题

| 现象 | 处理方法 |
|---|---|
| 打开网址显示 404 | 刚开启 Pages 时需要等 1～2 分钟；确认仓库是 public，并且 Settings → Pages 里选的分支是 `main`、目录是 `/ (root)`。 |
| 子页面 404，首页正常 | 链接末尾要带 `/`（如 `mythical-man-month/`），子目录里的文件必须叫 `index.html`。 |
| `gh repo create` 报仓库已存在 | 先改用 `git remote add origin https://github.com/Bao-SH/claude-explain-books.git`，再运行 `git push -u origin main`。 |
| `git push` 要求输入密码 | 运行 `gh auth setup-git`，让 git 使用 gh 的登录凭据。 |
| 不小心提交了 PDF / EPUB | 运行 `git rm --cached <文件名>`，提交后再推送；如果已经推送，需要清理历史，或者删除仓库后重新创建。 |
| 中文字体显示不同 | 页面从 Google Fonts 加载字体，加载不到时会用系统自带的中文字体，不影响功能。 |
