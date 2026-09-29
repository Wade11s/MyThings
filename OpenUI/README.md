# OpenUI 深度调研

> 单文件静态网页：`index.html`（内联 CSS/JS，零外部依赖，可离线双击打开）。
> 线上：https://wade11s.github.io/MyThings/OpenUI/

研究对象：[`thesysdev/openui`](https://github.com/thesysdev/openui) —— Thesys 开源的生成式 UI 框架与 **OpenUI Lang** 语言（MIT，2024-12 建仓）。

## 页面包含什么

- **交互图表（原生 SVG，无依赖）**：token 基准（4 格式 × 7 场景，可切「绝对 / 相对倍数 / 秒数」）、可靠性基准（6 模型 × 3 格式）、密度曲线、白屏数、失败归因、OUI-1 训练的「跷跷板」错误曲线、31 个模型的「有效率 × 成本」散点（含帕累托前沿）、npm 下载量。
- **流式渲染演示**：页面内嵌一个约 200 行的教学级 OpenUI Lang 迷你解析器，逐字流式解析真实 Lang 程序并渐进渲染（含前向引用骨架屏）；生成结束后切换下拉框可看到运行时本地重算、0 token。
- **两类对照动画**：JSON vs OpenUI Lang 的同一 TODO 应用；工具调用循环 vs 生成式 UI 的交互路径。

## 数据口径

| 数据 | 来源 | 时间 |
|---|---|---|
| token / 延迟基准（4 格式 × 7 场景） | 仓库 `benchmarks/` | 仓库快照 |
| 可靠性基准（46 brief × 6 模型 × 4 次 = 1,104 次/格式） | `docs/lib/benchmark-data.ts` + 官方博文 2026-08-17 | 2026-08 |
| 模型榜（31 个模型） | `docs/lib/benchmark-data.ts`（2026-08-24/25 运行） | 2026-08 |
| OUI-1 训练与得分 | 官方博文 2026-09-08 | 2026-09 |
| GitHub 指标（stars/forks/tags） | GitHub API | 2026-09-29 |
| npm 月度下载 | npm registry API（2026-08-29 → 2026-09-27） | 2026-09 |

> 注意：官方博文的汇总结构有效率（96.5 / 95.7 / 80.2）与站点数据文件逐模型汇总（约 96.9 / 95.6 / 82.8）略有差异，页面同时给出两种口径并说明采用哪种。基准为 Thesys **第一方自测**，作者已在脚注中主动披露；原始输出与评分脚本提交在 `thesysdev/generative-ui-bench`。

## 主要参考

- 仓库：`README.md`、`docs/content/docs/openui-lang/*`、`benchmarks/`、`ADOPTERS.md`、`docs/lib/benchmark-data.ts`
- 官方博客：Stop making AI write JSON（2026-04-10）· The State of Generative UI in 2026（2026-06-10）· Your LLM is not a query engine（2026-07-23）· Generative UI Reliability benchmark（2026-08-17）· Introducing OUI-1（2026-09-08）· Rewriting our Rust WASM Parser in TypeScript（2026-03-13）· SaaS 2.0: Beyond the Chatbar（2026-04-07）
- 社区：Hacker News 讨论 [48770133](https://news.ycombinator.com/item?id=48770133)（公开标准）/ [49613182](https://news.ycombinator.com/item?id=49613182)（OUI-1）
- 对照项目：`vercel-labs/json-render`、`google/A2UI`、`CopilotKit/OpenGenerativeUI`、AG-UI、MCP-Apps

详细链接见页面「参考来源」章节。
