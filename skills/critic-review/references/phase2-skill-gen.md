# Phase 2: Auto Skill Generation

> Optional enhancement for teams running complex, repetitive workflows.

## Concept

After a Subagent completes a task involving 5+ tool calls, a **review Subagent** evaluates the execution and potentially generates a reusable Skill.

## Workflow

1. **Detect**: After task completion, count tool calls. If ≥ 5, proceed.
2. **Extract**: Review Subagent summarizes the execution pattern into a Skill template.
3. **Patch**: If a similar Skill exists, generate a diff (patch) rather than rewriting.
4. **Store**: Save to the skills directory with metadata.

## Skill Template Format

```markdown
---
name: <skill-name>
description: "<trigger description>"
---

# <Skill Name>

## When to Use
<conditions>

## Steps
1. <step>
2. <step>

## Notes
<things learned from execution>
```

## Progressive Disclosure

- Default: only load Skill summary (frontmatter)
- On trigger: load full SKILL.md body
- On execution: load bundled scripts/references as needed

This prevents token explosion as the Skill library grows.
