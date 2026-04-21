# AI Agent Fusion Lab 🔬

> Disassembling three top-tier AI Agent frameworks — Hermes Agent, Slack Engineering's multi-role system, and OpenClaw — and fusing them into one more powerful system.

## What is this?

An **AI Agent architecture experiment**. My AI assistant (Jarvis) independently analyzed three world-class Agent frameworks:

| Framework | Core Capability | Stars |
|-----------|----------------|-------|
| [Hermes Agent](https://github.com/nousresearch/hermes-agent) | 4-layer memory + closed-loop learning (gets smarter over time) | 22k+ |
| [Slack Engineering](https://slack.engineering/) | Multi-role checks & balances + narrative coherence enforcement (anti-hallucination) | Internal |
| [OpenClaw](https://github.com/openclaw) | Multi-agent orchestration + security isolation | - |

**Key finding**: Each framework solves a completely different problem with zero feature overlap. Fused together, they form an Agent system that is **smarter, more reliable, and self-improving**.

## Architecture

```
[OpenClaw Main] ─── Orchestration Layer (decides what to do, delegates to whom)
    │
    ├── [Hermes-style Execution] ─── Execution Layer
    │       ├── Dynamic planning: Plan → Execute → Replan
    │       ├── 70+ tool calls with step logging
    │       └── Async background review + auto Skill generation
    │
    ├── [Slack-style Critic] ─── Verification Layer
    │       ├── Journal: structured decision log (6 entry types)
    │       ├── Review: 5-level credibility scoring
    │       └── Timeline: narrative coherence enforcement & pruning
    │
    └── [OpenClaw Audit] ─── Final Acceptance Layer
            └── Pass / Reject / Revise
```

**In plain English**: OpenClaw acts as the commander, Hermes handles execution and continuous improvement, Slack's Critic verifies accuracy.

## Repository Structure

```
ai-agent-fusion-lab/
├── README.md                              ← You are here
├── LICENSE                                ← MIT License
├── protocols/
│   └── critic-protocol.md                 ← Critic verification protocol (Phase 1 shipped)
├── docs/
│   ├── slack-agentic-context-analysis.md       ← Deep analysis of Slack's system
│   └── three-framework-comparison.md           ← Detailed 3-framework comparison
└── articles/
    ├── README-zh-CN.md                      ← Main article (Chinese - Zhihu/WeChat)
    ├── xiaohongshu.md                       ← Xiaohongshu (Little Red Book) version
    ├── zhihu-zsxq.md                        ← Zhihu / Zsxq (Knowledge Planet) version
    └── douyin.md                            ← Douyin (TikTok China) version
```

## 🛠️ Critic Protocol (Shipped)

The core deliverable of Fusion Phase 1 — an **execution result verification protocol**.

### Three Core Outputs

1. **Journal (Decision Log)**: 6 entry types — DECISION / FINDING / HYPOTHESIS / ACTION / CORRECTION / QUESTION
2. **Review (5-Level Scoring)**: Verified → Likely → Plausible → Misguided → Hallucinated
3. **One-Line Verdict**: PASS / PASS_WITH_NOTES / FAIL + credibility distribution

### Core Principles

- **No message history passed**: Critic only sees Journal + current result, never raw conversation
- **Stronger model for Critic**: Critic uses a more capable model than the execution agent
- **Narrative coherence priority**: Content contradicting the overall evidence chain is automatically downgraded

See [`protocols/critic-protocol.md`](protocols/critic-protocol.md) for full specification.

## 🗺️ Implementation Roadmap

- [x] **Phase 1** (Complete) — Introduce Critic role, define Journal + scoring rubric
- [ ] **Phase 2** (In Progress) — Auto Skill generation with patch-based iteration
- [ ] **Phase 3** (Planned) — Timeline narrative coherence integrator

## 📚 Top 3 Takeaways

If you only learn one thing from each framework:

🥇 **Slack's "Narrative Coherence Enforcement"** — The most counter-intuitive anti-hallucination design
🥈 **Hermes's "Async Background Review"** — Learning becomes the agent's instinct, not overhead
🥉 **Hermes's "Progressive Disclosure"** — An elegant solution to token explosion in long-term agents

## 🤝 Acknowledgments

- [Nous Research](https://github.com/nousresearch) — Hermes Agent
- [Slack Engineering](https://slack.engineering/) — Multi-role security investigation system
- [OpenClaw](https://github.com/openclaw) — Multi-agent orchestration platform

## 📄 License

MIT License — use, modify, and distribute freely.
