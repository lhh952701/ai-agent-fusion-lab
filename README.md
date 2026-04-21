# AI Agent Fusion Lab 🔬

> 把 Hermes Agent、Slack Engineering 多角色系统、OpenClaw 三大框架拆解后融合成一个更强的 AI Agent 系统。

## 📖 这是什么？

这是一个**AI Agent 架构实验项目**。我的 AI 助手（贾维斯）独立分析了三款顶级 AI Agent 框架：

| 框架 | 核心能力 | Stars |
|------|----------|-------|
| [Hermes Agent](https://github.com/nousresearch/hermes-agent) | 四层记忆 + 闭合学习回路（越用越强） | 22k+ |
| [Slack Engineering](https://slack.engineering/) | 多角色制衡 + 叙事一致性强制（防幻觉） | 企业内部 |
| [OpenClaw](https://github.com/openclaw) | 多智能体编排 + 安全隔离 | - |

**发现**：三者各自解决一个完全不同的问题，零功能重叠。融合后得到一个**既聪明又可靠还会越来越强**的 Agent 系统。

## 🏗️ 融合架构

```
[OpenClaw Main] ─── 编排层（决定做什么、分发给谁）
    │
    ├── [Hermes 式执行] ─── 执行层
    │       ├── 动态规划：Plan → Execute → Replan
    │       ├── 70+ 工具调用 + 步骤记录
    │       └── 异步后台复盘 + 自动生成 Skill
    │
    ├── [Slack 式 Critic] ─── 验证层
    │       ├── Journal：结构化决策日志（6 种条目）
    │       ├── Review：五级可信度评分
    │       └── Timeline：叙事一致性强制剪枝
    │
    └── [OpenClaw Audit] ─── 最终验收层
            └── 通过 / 打回 / 修正
```

**人话版**：OpenClaw 当指挥官，Hermes 负责把事做好并越做越好，Slack 的 Critic 负责检查做得对不对。

## 📁 项目结构

```
ai-agent-fusion-lab/
├── README.md                          ← 你在这里
├── LICENSE                            ← MIT 许可证
├── protocols/
│   └── critic-protocol.md             ← Critic 验证协议（Phase 1 已落地）
├── docs/
│   ├── slack-agentic-context-analysis.md    ← Slack 系统深度分析
│   └── three-framework-comparison.md        ← 三框架对比分析
└── articles/
    ├── README-zh-CN.md               ← 主文（知乎/公众号）
    ├── xiaohongshu.md                ← 小红书版
    ├── zhihu-zsxq.md                 ← 知识星球版
    └── douyin.md                     ← 抖音版
```

## 🛠️ Critic Protocol（已落地）

融合方案 Phase 1 的核心产出——**执行结果验证协议**。

### 三大核心输出

1. **Journal（决策日志）**：6 种条目类型（DECISION / FINDING / HYPOTHESIS / ACTION / CORRECTION / QUESTION）
2. **Review（五级评分）**：Verified → Likely → Plausible → Misguided → Hallucinated
3. **一句话裁定**：PASS / PASS_WITH_NOTES / FAIL + 可信度分布

### 核心原则

- **不传消息历史**：Critic 只看 Journal + 当前结果，不看原始对话
- **用更强模型**：Critic 用比执行 Agent 更强的模型
- **叙事一致性优先**：与整体证据链矛盾的内容自动降级

详见 [`protocols/critic-protocol.md`](protocols/critic-protocol.md)。

## 🗺️ 实施路径

- [x] **Phase 1**（已完成）— 引入 Critic 角色，定义 Journal + 评分标准
- [ ] **Phase 2**（进行中）— Skill 自动生成与补丁式迭代
- [ ] **Phase 3**（计划中）— Timeline 叙事一致性整合器

## 📚 推荐阅读

如果只从三个框架各学一个东西：

🥇 **Slack 的"叙事一致性强制"** — 防幻觉的最反直觉设计
🥈 **Hermes 的"异步后台复盘"** — 学习变成 Agent 的本能
🥉 **Hermes 的"渐进式披露"** — 解决 Token 爆炸的优雅方案

## 🤝 致谢

- [Nous Research](https://github.com/nousresearch) — Hermes Agent
- [Slack Engineering](https://slack.engineering/) — 多角色安全调查系统
- [OpenClaw](https://github.com/openclaw) — 多智能体编排平台

## 📄 许可证

MIT License — 自由使用、修改、分发。

---

*本文由 AI 助手贾维斯主笔，经人类审核发布。*
