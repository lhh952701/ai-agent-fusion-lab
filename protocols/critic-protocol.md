# Critic Protocol — Execution Result Verification Mechanism

> Inspired by: Slack Engineering's multi-Agent security investigation system
> Integrated into: Main → Ops (execute) → **Critic (score)** → Audit (accept)

## 1. When to Trigger Critic?

- After a Subagent completes any **substantive task** (not casual chat or simple Q&A)
- When the task involves **factual claims** (data, conclusions, recommendations)
- When the user explicitly requests verification

Do NOT trigger for: greetings, format conversions, simple file reads/writes, code completions, or other low-risk operations.

## 2. Critic's Three Outputs

### 2.1 Journal Entry (Decision Log)

Before scoring, Critic writes a Journal entry to `memory/journal.md`.

Format:
```
## [Timestamp] Task Summary
- **Type**: DECISION / FINDING / HYPOTHESIS / ACTION / CORRECTION / QUESTION
- **Decision**: What was chosen and why
- **Basis**: What information the decision was based on
- **Expected**: What the expected outcome was
- **Actual**: What actually happened
- **Variance**: Gap between expected and actual (if any)
- **Citations**: Key information sources
```

Journal entry types (6):
1. **DECISION** — A key decision made
2. **FINDING** — A fact discovered
3. **HYPOTHESIS** — A hypothesis proposed
4. **ACTION** — An action taken
5. **CORRECTION** — An error corrected
6. **QUESTION** — Something pending verification

### 2.2 Review Scoring (Evidence Chain Audit)

Each **factual claim** in the execution result is scored individually:

| Level | Label | Meaning | Action |
|-------|-------|---------|--------|
| 5 | **Verified** | Direct evidence supports it; can be trusted | Keep |
| 4 | **Likely** | Indirect evidence supports it; probably correct | Keep, cite source |
| 3 | **Plausible** | Logically coherent but no direct evidence | Keep, flag for verification |
| 2 | **Misguided** | Contradictory evidence or logical flaws | Prune or revise |
| 1 | **Hallucinated** | No evidence; contradicts known facts | Must delete |

Scoring criteria:
- Is there a traceable information source?
- Is the logic chain complete (premise → reasoning → conclusion)?
- Does it contradict previously recorded Journal facts?
- Is there over-inference (jumping from A to D, skipping B and C)?

### 2.3 One-Line Verdict

```
Critic Verdict: [PASS / PASS_WITH_NOTES / FAIL]
Reason: [one sentence]
Credibility Distribution: Verified X% | Likely X% | Plausible X% | Misguided X% | Hallucinated X%
```

## 3. Core Principles

### 3.1 No Message History
Even if the context window has room, Critic **never receives raw conversation history**.

It only receives:
- Journal (decision log)
- Current execution result under review
- Relevant file contents (read on demand)

**Why**: Accumulated old context biases Critic's evaluation of the current result.

### 3.2 Stronger Model for Critic
If available, Critic should use a more capable model than the execution Agent.
(e.g., execution uses Sonnet, Critic uses Opus)

### 3.3 Narrative Coherence Priority
An individual finding may be logically self-consistent, but if it contradicts the overall evidence chain narrative, it should be downgraded.

**Hallucinations can only survive if they are more narratively coherent than real observations** — exploit this in reverse to detect them.

## 4. Implementation

Critic does not require a separate process. In the execution flow, the Main Agent switches to Critic mode after completing a task:

1. Read `memory/journal.md` for historical decision context
2. Score factual claims in the current result using the rubric above
3. Append a new Journal entry to `memory/journal.md`
4. Output the verdict
5. If FAIL, automatically trigger a revision loop and re-score

## 5. Critic vs. Audit

| | Audit (existing) | Critic (new) |
|--|--|--|
| **Focus** | Security compliance, permission checks | Factual accuracy, logical consistency |
| **Trigger** | Every tool call | After task completion |
| **Output** | Pass / Reject | 5-level score + credibility distribution |
| **Accumulation** | None | Journal grows over time |
