# Phase 3: Timeline Narrative Coherence

> Optional enhancement for long-running investigations and research tasks.

## Concept

A periodic integration process that reads Journal + execution results, constructs a narrative timeline, and prunes contradictions.

## Workflow

1. **Collect**: Read all Journal entries since last integration.
2. **Construct**: Build a chronological timeline of findings and decisions.
3. **Check**: For each finding, verify narrative coherence with the overall timeline.
4. **Prune**: Downgrade or remove entries that contradict the narrative.
5. **Consolidate**: Output a clean, coherent summary.

## Narrative Coherence Rule

> "Hallucinations can only survive if they are more narratively coherent than real observations."

A single claim may be logically self-consistent, but if it contradicts the broader evidence chain, it is likely false. This is the most counter-intuitive but effective anti-hallucination mechanism.

## Implementation as Cron Job

For OpenClaw users, this can be scheduled as a daily cron job:

```
# Run timeline coherence check daily at 23:00
schedule: "0 23 * * *"
payload: "Read memory/journal.md, check narrative coherence, prune contradictions, save summary to memory/timeline-summary.md"
```

## Output Format

```
# Timeline Summary — YYYY-MM-DD

## Coherent Findings (retained)
- [Entry ID] Finding description (Verified/Likely)

## Contradictions Found (pruned)
- [Entry ID] Finding description — Reason: contradicts [Entry ID]

## Narrative Gaps
- [Topic] No evidence found for or against
```
