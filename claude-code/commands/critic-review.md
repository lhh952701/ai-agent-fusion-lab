Run a full Critic Protocol review on the task just completed.

## Steps

1. Identify every factual claim in the previous task output
2. Score each claim: Verified (5) / Likely (4) / Plausible (3) / Misguided (2) / Hallucinated (1)
3. For each claim, note: the claim, the score, and justification
4. Output the verdict table and one-line summary
5. Append a Journal entry to `memory/journal.md`
6. If any claim scored ≤ 2, propose corrections

## Output Format

```
## Critic Review

| # | Claim | Score | Justification |
|---|-------|-------|---------------|
| 1 | ... | 5/4/3/2/1 | ... |

**Verdict**: PASS / PASS_WITH_NOTES / FAIL
**Credibility**: Verified X% | Likely X% | Plausible X% | Misguided X% | Hallucinated X%

[If FAIL: proposed corrections below]
```
