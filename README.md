# AI Agent Fusion Lab 🔬

> **I designed a unified Agent architecture** by fusing the best ideas from three world-class frameworks — and built production-ready plugins for both [OpenClaw](https://github.com/openclaw) and [Claude Code](https://claude.ai/code) to make it usable today.

## The Problem

Most AI Agents suffer from three systemic flaws:

1. **Amnesia** — They forget everything between sessions. Teach them something today, they relearn it tomorrow.
2. **Blind spots** — They can't tell when they're wrong. No self-verification, no fact-checking.
3. **Context collapse** — Long conversations degrade. Token explosion, contradictions, task drift.

## The Solution: Fusion Architecture

I analyzed three frameworks that each solve **one** of these problems perfectly — with zero overlap:

| Framework | Solves | Key Innovation |
|-----------|--------|----------------|
| [Hermes Agent](https://github.com/nousresearch/hermes-agent) (22k+ ⭐) | Amnesia | 4-layer memory + closed-loop learning |
| [Slack Engineering](https://slack.engineering/) | Blind spots | Multi-role Critic + narrative coherence |
| [OpenClaw](https://github.com/openclaw) | Context collapse | Structured channels replace message history |

**My fusion insight**: Wire them together — memory feeds the critic, the critic feeds the orchestrator, and nothing relies on raw message history.

### Architecture

```
                    ┌─────────────────────────────┐
                    │     Orchestrator (OpenClaw)   │
                    │   Decides what, delegates who  │
                    └──────┬──────────┬────────────┘
                           │          │
              ┌────────────▼──┐  ┌────▼─────────────┐
              │   Executor     │  │     Critic        │
              │ (Hermes-style) │  │ (Slack-style)     │
              │                │  │                   │
              │ • Plan→Exec    │  │ • 5-level score   │
              │ • Auto Skill   │  │ • Journal log     │
              │ • Async review │  │ • Narrative check │
              └────────────────┘  └───────────────────┘
```

## What's in This Repo

### 🧩 Installable Plugins

| Plugin | Platform | What it does |
|--------|----------|-------------|
| [`skills/critic-review/`](skills/critic-review/) | OpenClaw | Post-task factual verification — scores claims, logs decisions, outputs verdict |
| [`claude-code/`](claude-code/) | Claude Code | CLAUDE.md + slash commands for the same Critic Protocol |

**Quick start — OpenClaw:**
```bash
# Copy the skill to your OpenClaw skills directory
cp -r skills/critic-review/ ~/.openclaw/skills/
# Restart OpenClaw — Critic triggers automatically after substantive tasks
```

**Quick start — Claude Code:**
```bash
# Copy into your project
cp -r claude-code/ .claude/
# Use /critic-review after completing any task
```

### 📖 Documentation

| File | Description |
|------|-------------|
| [`protocols/critic-protocol.md`](protocols/critic-protocol.md) | Full Critic Protocol specification |
| [`docs/slack-agentic-context-analysis.md`](docs/slack-agentic-context-analysis.md) | Deep analysis of Slack's investigation system |
| [`docs/three-framework-comparison.md`](docs/three-framework-comparison.md) | Detailed 3-framework comparison |

### ✍️ Articles (Chinese)

Published on four platforms — [`articles/`](articles/) contains the source text for each:

| Platform | File |
|----------|------|
| Zhihu / WeChat | `articles/README-zh-CN.md` |
| Xiaohongshu | `articles/xiaohongshu.md` |
| Knowledge Planet | `articles/zhihu-zsxq.md` |
| Douyin | `articles/douyin.md` |

## How the Critic Protocol Works

### Three Outputs

1. **Journal** — Structured decision log with 6 entry types (DECISION, FINDING, HYPOTHESIS, ACTION, CORRECTION, QUESTION)
2. **Review** — 5-level credibility scoring for every factual claim
3. **Verdict** — PASS / PASS_WITH_NOTES / FAIL + credibility distribution

### The Anti-Hallucination Secret

> *"Hallucinations can only survive if they are more narratively coherent than real observations."*

A single claim may look correct in isolation. But if it contradicts the overall evidence chain — the Journal — it gets downgraded automatically. This is the most counter-intuitive but effective anti-hallucination mechanism I've found.

### Core Principles

- **No message history passed** — Critic only sees Journal + current result
- **Stronger model for Critic** — Verification uses more capable model than execution
- **Narrative coherence priority** — Contradictions with evidence chain → auto downgrade

## Implementation Roadmap

- [x] **Phase 1** — Critic Protocol + Journal format + 5-level scoring ✅
- [x] **Phase 1b** — OpenClaw Skill + Claude Code plugin ✅
- [ ] **Phase 2** — Auto Skill generation from repeated task patterns
- [ ] **Phase 3** — Timeline narrative coherence integrator (cron-based)

## Top 3 Takeaways

If you only learn one thing from each framework:

🥇 **Slack's "Narrative Coherence Enforcement"** — Check claims against the whole evidence chain, not just in isolation
🥈 **Hermes's "Async Background Review"** — Learning becomes the agent's instinct, not overhead
🥉 **Hermes's "Progressive Disclosure"** — Only load Skill summaries by default; full content on demand

## Acknowledgments

- [Nous Research](https://github.com/nousresearch) — Hermes Agent
- [Slack Engineering](https://slack.engineering/) — Multi-role investigation system
- [OpenClaw](https://github.com/openclaw) — Multi-agent orchestration platform

## License

MIT — use, modify, and distribute freely.
