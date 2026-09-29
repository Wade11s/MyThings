# AGENTS.md

本仓库是**静态网页合集**：每个文件夹 = 一个可独立访问的**自包含页面**，由 GitHub Pages 托管，供随时随地用浏览器打开。

- 线上站点：https://wade11s.github.io/MyThings/
- 仓库：`git@github.com:Wade11s/MyThings.git`（public，`main` 分支）
- Pages：legacy 构建，源 = `main` 分支根目录（`/`），push 后约 1 分钟自动部署
- 本地：直接双击打开 `index.html` 即可离线浏览，无需任何构建

## 不变量

改动前必读。这些是决定站点能否工作的硬约束。

- **自包含页面** —— 每个 `<文件夹>/index.html` 是单文件、可离线打开的静态网页：CSS/JS 内联在文件内，用系统字体，零外部依赖、零构建步骤。Pages 托管与离线双击都依赖这一点。
- **文件夹即路由** —— 文件夹名就是 URL 路径：`Jev/index.html` → `/MyThings/Jev/`。所以每个页面的入口文件必须命名为 `index.html`。
- **根 index.html 是地图** —— 它是站点首页，手工维护所有子页面入口。新增或删除页面时必须同步它，否则页面对外不可见。
- **内容公开** —— 本仓库 public，仓库内一切内容全球可见，并可能被搜索引擎收录；私密材料另放私有仓库。
- **简体中文、暖色纸感风格** —— 页面与文档都用简体中文，视觉风格对齐现有 `Jev/index.html`（衬线标题、暖色卡片）。
- **纯静态形态** —— 仓库根目录保持「HTML + 说明文档」。不引入框架、包管理器或 CI 构建。
- **环境** —— 仓库位于 macOS `~/Desktop`（疑似 iCloud 同步目录）。push 正常；若 git 报出与用户操作无关的仓库内部错误，先把仓库移到非同步目录再操作。

## 新增一个页面

1. `mkdir <名称>`，写入自包含的 `<名称>/index.html`。
2. 在根 `index.html` 的 `.wrap` 内照抄 Jev 卡片格式，加一张指向 `<名称>/` 的入口卡片。
3. 在根 `README.md` 的表格里补一行 `<名称>/`。
4. `git add . && git commit -m "add <名称>" && git push`。
5. 验证部署：`gh api repos/Wade11s/MyThings/pages/builds/latest --jq .status` 轮询到 `built`（间隔约 10 秒），再 `curl -sIL https://wade11s.github.io/MyThings/<名称>/`。

完成标准：`https://wade11s.github.io/MyThings/<名称>/` 返回 HTTP 200，且页面内容与本地一致。

## 参考

- 仓库说明与人类快速上手：`README.md`
- 页面内容来源与调研结论（示例页面）：`Jev/README.md`
- 站点配置：`gh api repos/Wade11s/MyThings/pages`
