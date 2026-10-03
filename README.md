# MyThings

个人静态站点仓库 —— 每个文件夹是一个可独立访问的网页，通过 GitHub Pages 渲染：

**🔗 https://wade11s.github.io/MyThings/**

根 `index.html` 就是全站**目录页**（可搜索 / 标签筛选的卡片列表）；每个子页面顶部 nav 右侧都有 `← 首页` 按钮，点 logo 也可回首页。

| 文件夹 | 内容 | 链接 |
|---|---|---|
| `CuaDriver/` | Cua Driver 自动化测试：ok 不是证据，夹具 E2E 与 verify_state | [/CuaDriver/](https://wade11s.github.io/MyThings/CuaDriver/) |
| `Jev/` | Jev 模型深度调研 · 单文件交互式网页 | [/Jev/](https://wade11s.github.io/MyThings/Jev/) |
| `OpenUI/` | OpenUI 生成式 UI 框架深度调研 · 交互图表 + 流式渲染演示 | [/OpenUI/](https://wade11s.github.io/MyThings/OpenUI/) |
| `Karpathy/` | Karpathy 最新长推（2026-10-02）深度解析 · 四级输出格式阶梯可交互 | [/Karpathy/](https://wade11s.github.io/MyThings/Karpathy/) |

## 新增一个网页文件夹

```bash
mkdir NewFolder
# 放入自包含的 NewFolder/index.html（含 nav 里的 <a class="home-btn" href="../">← 首页</a>）
git add . && git commit -m "add NewFolder" && git push
```

推送后约 1 分钟内生效：`https://wade11s.github.io/MyThings/NewFolder/`

> 约定：每个文件夹放一个 `index.html`（内联 CSS/JS、无外部依赖），Pages 即自动托管；首页（根 `index.html`）列出所有入口，且每个子页必须有返回首页的按钮。
