# Agent Description Quality Rubric

Score agent descriptions on a 0-100 scale. Use this to audit existing agents or evaluate drafts before running evals.

---

## Scoring Criteria

| Criterion | Weight | What It Measures |
|-----------|--------|-----------------|
| Domain knowledge injection | 25 | Checklists, anti-patterns, decision tables present and specific |
| False positive prevention | 20 | Anti-patterns list, "verify before claiming" rules, graduated confidence |
| Conciseness | 15 | No filler, every line earns its place, no attention tax |
| Correct content types | 15 | Knowledge content present, process content absent |
| Length compliance | 10 | Within target range for agent type |
| Identity clarity | 10 | Clear role, behavioral stance, domain scope |
| Currency | 5 | Domain facts are current and version-pinned, not stale |

**Total: 100 points**

---

## Detailed Rubric

### Domain Knowledge Injection (25 points)

| Score | Description |
|-------|-------------|
| 22-25 | Rich, specific checklists naming concrete failure patterns. Decision tables for non-obvious choices. Multiple domain areas covered with key bullets. |
| 16-21 | Good checklists but some items are vague ("check for performance issues" instead of "N+1 queries in loops"). Decision tables present but incomplete. |
| 10-15 | Some domain knowledge but mostly generic. Few specific failure patterns named. Missing entire domain areas. |
| 4-9 | Minimal domain knowledge. Mostly generic advice the model already knows. |
| 0-3 | No domain-specific content. Could apply to any task. |

### False Positive Prevention (20 points)

| Score | Description |
|-------|-------------|
| 18-20 | Explicit anti-patterns list. "Verify before claiming" rules. Graduated confidence tiers. Context-aware exceptions (e.g., "SHA256 for checksums is fine"). |
| 13-17 | Some false positive prevention but incomplete. Has verification rules but missing common false positive traps. |
| 7-12 | Minimal false positive awareness. No anti-patterns list. May have one or two "don't flag" items. |
| 1-6 | No false positive prevention. Agent will flag everything it can find. |
| 0 | Actively encourages false positives (e.g., "flag anything suspicious"). |

### Conciseness (15 points)

| Score | Description |
|-------|-------------|
| 13-15 | Every line maps to a content type that adds capability. No filler, no repetition, no adjective lists. |
| 9-12 | Mostly lean but has some filler (repeated points, soft adjectives, unnecessary context). |
| 5-8 | Significant filler. Multiple lines that don't add domain knowledge or prevent mistakes. |
| 1-4 | Mostly filler. Reads like a job description rather than a technical specification. |
| 0 | Pure filler. Adjective lists, motivational language, no substance. |

### Correct Content Types (15 points)

| Score | Description |
|-------|-------------|
| 13-15 | All content is knowledge (checklists, anti-patterns, decision tables, constraints). Zero process instructions or rigid templates. |
| 9-12 | Mostly knowledge content. Minor process elements that could be cut without loss. |
| 5-8 | Mixed. Has valuable knowledge content alongside process sections, templates, or workflow steps. |
| 1-4 | Dominated by process. Step-by-step workflows, rigid output templates, completion criteria. |
| 0 | All process, no knowledge. Reads like an SOP, not a domain reference. |

### Length Compliance (10 points)

| Score | Description |
|-------|-------------|
| 9-10 | Within target range for agent type. Review: 90-120, Implementation: 60-90, Research: 30-50. |
| 6-8 | Within 20 lines of target range. |
| 3-5 | More than 20 lines outside target. Either too long (attention decay) or too short (missing checklists). |
| 1-2 | Significantly outside range. Over 150 lines (decay zone) or under 30 lines (missing substance). |
| 0 | Extreme outlier. Over 200 lines or under 10 lines. |

### Identity Clarity (10 points)

| Score | Description |
|-------|-------------|
| 9-10 | Clear 1-2 line role statement. Behavioral stance obvious. Domain scope unambiguous. |
| 6-8 | Role is clear but takes too many lines, or scope is slightly ambiguous. |
| 3-5 | Role is vague or missing. Reader can infer it from content but it's not stated. |
| 1-2 | Confusing identity. Multiple conflicting roles or unclear scope. |
| 0 | No identity framing at all. |

### Currency (5 points)

| Score | Description |
|-------|-------------|
| 5 | Domain facts are current. Version numbers included where relevant. No stale guidance. |
| 3-4 | Mostly current. Minor items may be outdated but nothing dangerous. |
| 1-2 | Some stale facts that could cause incorrect guidance. |
| 0 | Contains known-wrong guidance (e.g., wrong JDK version recommendations, deprecated APIs treated as current). |

---

## Score Interpretation

| Range | Assessment | Action |
|-------|-----------|--------|
| 85-100 | Excellent | Ship it. Minor polish only. |
| 70-84 | Good | Targeted improvements. Usually needs better checklists or anti-patterns. |
| 50-69 | Needs work | Significant gaps. Likely has process content that should be knowledge content. |
| 30-49 | Poor | Major rewrite needed. Probably over-constrains or lacks domain knowledge. |
| 0-29 | Harmful | May perform worse than bare model. Consider starting fresh. |

---

## Quick Audit Checklist

Run through these yes/no checks for a fast assessment:

- [ ] Does it have domain-specific checklists? (not generic advice)
- [ ] Does it have an anti-patterns or false positive list?
- [ ] Is every line one of the 7 valid content types?
- [ ] Are there zero process/workflow sections?
- [ ] Are there zero rigid output templates?
- [ ] Is it within the length target for its type?
- [ ] Does the identity framing fit in 1-2 lines?
- [ ] Are domain facts current and version-pinned?
- [ ] Would removing any single line reduce quality? (if no, that line is filler)
