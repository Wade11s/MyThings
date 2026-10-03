# Cua Driver 自动化测试

调研产出：`index.html` —— 单文件交互式说明页（可离线打开，无需构建、无外部依赖）。

```bash
open index.html   # macOS
```

## 核心结论（TL;DR）

**Cua Driver 是计算机使用代理的界面层，不是沙箱。** 它读窗口的无障碍树和截图，再点击、输入、滚动。自动化测试要分成两件不同的事：仓库怎么证明驱动本身，以及你怎么用驱动去证明自己的应用。

两件事共用一条判据：**工具返回 `ok` 不是证据。**

- **测驱动自己**有三层。单元 / 协议 / schema / 边界 fuzz 不打开夹具，也不证明真的点到了应用。规范 E2E 在真实桌面会话里驱动**源码构建**的夹具（Electron、Tauri，以及各 OS 的原生工具包），用夹具或桌面自己拥有的状态做 oracle。已安装应用只是补充：macOS 的 Calculator / TextEdit 在规范车道里，LibreOffice 是可选套件。Rust 拥有场景和断言；没有第二套 Python E2E 矩阵。
- **一格**是目录里的一个单元格：动作 × 寻址（AX / PX）× 投递（后台 / 前台）× 范围（窗口 / 桌面）× 表面。通过条件是：夹具或桌面状态按契约变化；若测后台，焦点、叠放、真光标和前台应用还得不动。契约声明的精确拒绝也算通过。静默成功不算。规范车道设 `CUA_E2E_FORBID_SKIPS=1`。行为格开跑前，哨兵必须先抓住故意注入的泄漏，否则绿灯不可信。
- **共享 Web 目录**（`main`，2026-10-03 读取）：每个夹具应用 40 个带证据的格子。Windows / Linux 跑 Electron + Tauri，共 80 格；macOS 再加 WKWebView，共 120 格。这是目录规模，不是「本机 0.31.0 刚刚全绿」的证书。
- **用驱动测自己的应用**：先观察，再用本次快照的 `element_token` 行动，然后用 `verify_state` 或你自己的外部标记收口。`satisfied` / `unsatisfied` / `unknown` 里，`unknown` 永远不是通过。`exists: false` 会被 schema 拒绝，因为元素遍历尚未在每个平台被证明穷尽。`stable_samples` 默认 2。驱动可以附上截图，但不解释这张图。
- **不要用两次 `cua-driver call` 传递 token。** 公开文档写明一次性 `call` 各有一个一次性会话；结束后的 token 失效。跨步测试要用同一条 MCP 连接或进程内 SDK。一条桌面同时只有一个控制者。
- **录制是证据，不是耐久脚本。** `replay_trajectory` 按原参数重放，不会刷新 pid、window id、element token 或浏览器 ref。取消、部分成功、未知结果不许盲目重放。

## 网页结构（index.html）

00 两条测试 → 01 交互判官 → 02 三层 → 03 一格怎么跑 → 04 矩阵切片 → 05 夹具与 oracle → 06 证据 → 07 平台边界 → 08 自己的测试环 → 09 `verify_state` 试验台 → 10 录制与回放 → 11 边界 → 12 来源

页内三个交互都是**契约模拟**，不会调用本机的 `cua-driver`。

## 主要参考来源

**仓库内测试架构（一手）**，2026-10-03 读取 `trycua/cua` `main`：

- `libs/cua-driver/docs/test-matrix.md`
- `libs/cua-driver/docs/test-harnesses-guide.md`
- `libs/cua-driver/rust/crates/cua-driver-e2e/tests/README.md`
- `libs/cua-driver/tests/fixtures/README.md`
- `libs/cua-driver/README.md`
- 根目录 `Development.md`

**公开文档**：

- [Platform Support](https://cua.ai/docs/reference/cua-driver/platform-support)
- [How Cua Driver works](https://cua.ai/docs/cua-driver/concepts/how-cua-driver-works)（含「`ok` 不是证据」的目录段落）

`Development.md` 指向 `https://cua.ai/docs/cua-driver/concepts/how-cua-driver-is-validated`。2026-10-03 抓取该 URL 时被重定向到 *How Cua Driver works*，因此验证模型的完整分层以仓库内贡献者文档为准，不把搜索摘要当成正文。

**本机契约**：`cua-driver 0.31.0` 的 `describe click`、`describe verify_state`，以及同版本技能包里的录制 / 验证说明。技能包版本号标识其来源发布，不等同于「该发布已通过整份 E2E 目录」。

> 平台账本会随合成器和工具包继续变。Hyprland 隔离输入的应用资格（LibreOffice Calc `26.2.5-3`、Inkscape `1.4.4-6`）是 2026-09-07 的带日期证据，不是对任意 Hyprland 桌面的认证。
