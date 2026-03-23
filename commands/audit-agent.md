---
description: Audit existing agent descriptions for quality issues. Scores each agent against the empirical quality rubric, flags anti-patterns, and suggests specific improvements.
allowed-tools: Read, Glob, Grep
---

# Audit Agent Descriptions

Scan all agent descriptions in `.claude/agents/` and score them against the quality rubric.

## Steps

### 1. Find all agent files

Glob for `.claude/agents/*.md` in the current project. List what you find.

### 2. Classify each agent

For each agent file, determine its type based on content:
- **Review/audit**: code review, security review, architecture review (target: 90-120 lines)
- **Implementation**: coding, refactoring, migration, TDD (target: 60-90 lines)
- **Research/analysis**: research, analysis, documentation (target: 30-50 lines)

### 3. Score against the quality rubric

For each agent, score these criteria (see `references/quality-rubric.md` for detailed rubric):

| Criterion | Weight | Score 0-max |
|-----------|--------|------------|
| Domain knowledge injection | 25 | Specific checklists? Anti-patterns? Decision tables? |
| False positive prevention | 20 | Anti-patterns list? Verify-before-claiming rules? |
| Conciseness | 15 | Every line earns its place? No filler? |
| Correct content types | 15 | Knowledge content present? Process content absent? |
| Length compliance | 10 | Within target range for agent type? |
| Identity clarity | 10 | Clear role in 1-2 lines? |
| Currency | 5 | Domain facts current? Version-pinned? |

### 4. Flag anti-patterns

Check each agent for these known problems (see `references/anti-patterns.md`):

- [ ] Rigid output templates (mandated format sections)
- [ ] "Be thorough" or adjective lists
- [ ] Binary verification gates that may cause self-censoring
- [ ] Step-by-step process/workflow sections
- [ ] Trigger conditions, completion criteria, success metrics
- [ ] Generic knowledge the model already has
- [ ] Stale domain facts (check version numbers)

### 5. Generate report

For each agent, output:

```
## {agent-name}.md ({type}, {line_count} lines)

Score: {total}/100

| Criterion | Score | Notes |
|-----------|-------|-------|
| Domain knowledge | X/25 | ... |
| False positive prevention | X/20 | ... |
| Conciseness | X/15 | ... |
| Correct content types | X/15 | ... |
| Length compliance | X/10 | ... |
| Identity clarity | X/10 | ... |
| Currency | X/5 | ... |

Anti-patterns found:
- {list any detected anti-patterns}

Top improvements:
1. {most impactful change}
2. {second most impactful}
3. {third most impactful}
```

### 6. Summary

End with a summary table ranking all agents by score, and highlight which agents need the most attention. Suggest the order to improve them (worst-scoring first, one at a time).
