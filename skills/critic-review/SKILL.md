---
name: critic-review
description: "Post-task factual verification protocol. Triggers after Subagent completes substantive tasks involving factual claims (data, conclusions, recommendations). Implements 5-level credibility scoring (Verified/Likely/Plausible/Misguided/Hallucinated), structured Journal logging (6 entry types), and one-line verdict (PASS/PASS_WITH_NOTES/FAIL). Inspired by Slack Engineering's multi-agent investigation system. Keywords: critic, review, verify, fact-check, hallucination, journal, credibility, score."
---

# Critic Review — Post-Task Factual Verification

## When to Trigger

- Subagent completes any **substantive task** (not chat/Q&A)
- Task involves **factual claims** (data, conclusions, recommendations)
- User explicitly requests verification

**Skip**: greetings, format conversions, simple file ops, code completions.

## Workflow

### Step 1: Load Journal Context

Read `memory/journal.md` for historical decisions. If it doesn't exist, create it with the header:
```
# Journal — Decision Log

> Auto-maintained by Critic Protocol
```

### Step 2: Score Each Factual Claim

For every factual claim in the execution result, assign a level:

| Level | Label | Action |
|-------|-------|--------|
| 5 | Verified | Keep — direct evidence |
| 4 | Likely | Keep + cite source — indirect evidence |
| 3 | Plausible | Keep + flag — no direct evidence |
| 2 | Misguided | Prune/revise — contradictory evidence |
| 1 | Hallucinated | Must delete — contradicts known facts |

**Scoring criteria:**
- Traceable source? Logic chain complete (premise→reasoning→conclusion)?
- Contradicts Journal history? Over-inference (A→D skipping B,C)?

### Step 3: Write Journal Entry

Append to `memory/journal.md`:

```
## [YYYY-MM-DD HH:MM] Task Summary
- **Type**: DECISION / FINDING / HYPOTHESIS / ACTION / CORRECTION / QUESTION
- **Decision**: What was chosen and why
- **Basis**: What information the decision was based on
- **Expected**: Expected outcome
- **Actual**: Actual outcome
- **Variance**: Gap (if any)
- **Citations**: Key sources
```

### Step 4: Output Verdict

```
Critic Verdict: [PASS / PASS_WITH_NOTES / FAIL]
Reason: [one sentence]
Credibility Distribution: Verified X% | Likely X% | Plausible X% | Misguided X% | Hallucinated X%
```

### Step 5: Handle FAIL

If FAIL: identify hallucinated/misguided claims, trigger revision, re-score.

## Core Principles

1. **No message history** — Critic only sees Journal + current result
2. **Stronger model** — Use more capable model if available
3. **Narrative coherence** — Contradicting overall evidence chain → downgrade

## Advanced: Progressive Enhancement

For Phase 2+ users, see [references/phase2-skill-gen.md](references/phase2-skill-gen.md) for auto Skill generation, and [references/phase3-timeline.md](references/phase3-timeline.md) for narrative coherence integration.
