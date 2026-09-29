# Jev 模型深度调研

调研产出：`index.html` —— 单文件交互式解读网页（可离线打开，无需构建、无外部依赖）。

```bash
open index.html   # macOS
```

## 核心结论（TL;DR）

**Jev 是 TypeSafe AI 于 2026-09-15 发布的首个「系统一模型」（System One Model）** —— 不生成文本，
输入 state（文本/JSON）+ 一组类型化问题（Choice / Score / Noul 三种原语），单次前向并行返回
带校准概率的结构化决策，供软件直接消费。

- **出发点**：创始人 Diogo Almeida 是 OpenAI RLHF / InstructGPT 核心作者。反思 RLHF 让模型
  「过度自信、讨好人类、不可靠」；Agent 循环里最高频的原子判断（路由/校验/守门）每天都在
  消耗完整 LLM 调用（3–329s、输出 5 倍价、可幻觉）。机器间通信本就不需要自然语言。
- **命名**：卡尼曼「系统一/系统二」+ 杰文斯悖论（效率↑ → 总消耗↑）：单次判断便宜 1–2 个
  数量级，判断总量将暴涨数个数量级，抽样审查 → 全量实时判断。
- **原理**：跳过自回归，概率分布直接「长」在答案字段上；训练方法 RLCD（面向校准决策的强化
  学习），优化「概率是否诚实」（官方 ECE 0.0313 / 1200 道 MMLU，自评）；答案空间请求时锁死，
  schema 违规恒为 0 —— 但「保证接口不保证真相」，仍可能选错合法选项。
- **性能**：官方峰值快 193.6× / 省 444.6×（自测，官方自称偏高端）；独立审计实测快 5–25×、
  海外端到端 1.6–3.7s；独立基准 49 项任务 42 项≥基线（LogiQA 0.77/0.59、WinoGrande
  0.89/0.66、MMLU 0.94/0.87）；中文偏弱（约 64–65%）；非确定性漂移小（中位 0.010）。
- **价格**：输入 $0.042/MTok，输出免费；一美分 ≈ 240 次单问题决策。
- **落地**：Vercel 首日 ~13% 付费团队接入（平台史上最快）；Cloudflare / OpenRouter /
  LangChain 同步上架；48h 内 40+ 社区项目（pi-warden 护栏、Browser Use、邮件分诊 1700 封
  $0.18、PR 预筛 1000 个 $0.07、垃圾邮件零样本 98.3%、Doom 实时 ~$7/h）。
- **翻车案例**：交易机器人亏损 $31,680；Monad 做市大幅回撤；比特币信号失效；伦理题测试
  显示模型内置倾向会覆盖指令优先级。教训：概率 ≠ 执行权，不可逆操作必须有决策闸门。
- **三条使用前提**：① 答案空间可事先定义；② 问题可拆成原子判断；③ 错误可验证可回滚。

## 网页结构（index.html）

00 速览 → 01 出发点 → 02 命名思想 → 03 定位 → 04 原理（三原语/非自回归/RLCD/零幻觉辨析/
非确定性）→ 05 交互演示（3 个场景模拟）→ 06 性能（官方 vs 独立实测对照）→ 07 案例
（时间线 + 12 个成功案例 + 4 个翻车案例）→ 08 边界（jaggedness 清单 + 三前提）→ 09 启示
（决策层独立 / 校准概率=自动化开关 / 杰文斯效应 / 概率≠执行权）→ 10 参考资料

## 主要参考来源（仅一手资料，不含新闻媒体转述）

**官方与文档**：TypeSafe 发布博客与评测站（typesafe.ai、evals.typesafe.ai）、typesafe-ai/skills
**深度访谈**：Latent Space 播客《Jev: System One models for Prod, not God — with Diogo Almeida》（2h20m）
**独立博客（英文）**：Simon Willison、Arize（Laurie Voss）、Sean Goedecke、Firecrawl、Flavio Copes、MindStudio、Sébastien Dubois、Atharva Shah
**工程团队**：LangChain（Harness + LangGraph 两篇）、Vercel KB、AI SDK Evaluation 文档、Cloudflare Workers AI 文档、OpenRouter
**独立实测**：Every（Dan Shipper）、Data Science Collective 49 任务对比、NearHere 审核实测、explainx 事实核查
**社区与社交原帖**：HN 发布帖（1,984 赞）与定义之争帖、Show HN JevBench、r/Artificials 交易亏损复盘、r/PiCodingAgent pi-warden、X @gregpr07
**开源仓库**：browser-use/jev-ultrafast、APUS-AI-Lab/fast-browser-use、shantanugoel/mario-jev、drillan gist、cobanov/awesome-jev
**中文深度（独立博客）**：字与码 zicode（中文实测与翻车案例汇总）

> 数据截至 2026-09-28；官方数字均为厂商自测口径，网页中已搭配独立实测对照。
