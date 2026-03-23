# Agent Description Design Guide

Condensed reference for writing effective agent descriptions, empirically tested across multiple iterations and agents.

---

## DO: Content That Adds Capability

### 1. Domain checklists (highest value)

Specific failure patterns to check for. Knowledge injection.

Empirically, domain checklists are the single highest-impact content type. Removing them causes the largest quality regressions; restoring them recovers the full loss.

```
Good: "N+1 queries: fetching related data in a loop instead of JOIN/batch"
Good: "Missing dependency arrays in useEffect/useMemo/useCallback"
Bad:  "Check for common performance issues" (too vague)
```

### 2. Anti-patterns and false positive lists

What NOT to flag. Precision boosters.

"Grep before claiming X is missing" is consistently the single most impactful line across all agents tested.

```
Good: "SHA256 for checksums is fine. Only flag for password hashing"
Good: "Before claiming 'missing error handling' -- check the caller"
```

### 3. Decision tables

Compact tables encoding decision logic the model gets wrong.

```
Good: | App Type | Focus Areas         | Lower Priority      |
      | REST API | Auth, input valid.  | XSS (no HTML)       |
      | Web App  | XSS, CSRF, session  | CLI injection        |
```

### 4. Knowledge activation triggers

One-line section headers that activate latent knowledge.

Broad domain headers trigger coverage of topics the model would otherwise skip. A few well-chosen section headers with 2-3 key bullets each can outperform longer, more detailed instructions.

```
Good: "## Authentication & Security" + 2-3 key bullets
```

### 5. Behavioral constraints

Rules preventing known bad behaviors.

Behavioral constraints that enforce a specific discipline (like test-first development) produce significant, measurable lift over the bare model.

```
Good: "Write the test. Run it. Confirm FAILS. Only then implement"
Good: "NEVER modify a failing test to make it pass"
```

### 6. Identity framing (1-2 lines max)

```
Good: "You are a senior code reviewer ensuring high standards of code quality and security."
```

### 7. Graduated confidence (for review/audit agents)

Binary "prove it or don't report it" requirements cause the model to suppress its most dangerous findings -- exactly the ones hardest to prove without runtime access.

```
Good: "Label: CONFIRMED (proven) / LIKELY (pattern match) / POSSIBLE (suspicious). Report ALL."
Bad:  "Only report vulnerabilities you can prove are reachable."
```

---

## DON'T: Content That Subtracts Capability

| Anti-Pattern | Why It Fails | Impact |
|-------------|----------|--------|
| Rigid output templates | Model spends tokens on format compliance instead of analysis | Significant quality regression |
| Workflow/process sections | Attention tax, no knowledge added | Consistent regressions |
| "Be thorough" instructions | Redundant -- model is already proactive | Zero measured effect |
| Trigger conditions / completion criteria | For humans managing processes, not models doing work | Zero benefit, wastes lines |
| Generic knowledge model already has | Noise competing for attention with task-specific content | Negligible lift |
| Adjective lists | Describe qualities but don't teach capabilities | Zero lift over bare model |
| Batch changes to multiple agents | Interaction effects between changes are unpredictable | Regressions even when individual changes would improve |
| Binary verification gates | Model suppresses valid findings it can't fully prove | Quality regression on dangerous findings |

---

## Length Targets

| Agent Type | Target Lines | Rationale |
|-----------|-------------|-----------|
| Review/audit | 90-120 | Domain checklists ARE the product |
| Implementation | 60-90 | Needs guardrails, not tutorials |
| Research/analysis | 30-50 | Bare model ties most instruction sets |

**Ceiling: ~150 lines.** Instruction-following degrades exponentially on Sonnet beyond this.

**Floor: ~40 lines.** Below this, you lose anti-patterns and severity calibration.

**More lines != better.** Content type matters more than quantity. A shorter, focused agent consistently outperforms a longer, sprawling one.

---

## Content Audit Checklist

For every line in an agent description, it must be one of:

| Content Type | Test |
|-------------|------|
| Domain checklist item | Names a specific failure pattern? |
| Anti-pattern / false positive | Prevents a known mistake? |
| Decision table row | Encodes choice logic? |
| Knowledge activation trigger | Opens a topic the model skips? |
| Behavioral constraint | Stops a known bad behavior? |
| Non-obvious domain fact | Model gets this wrong by default? |
| Identity framing | Sets the role? (1-2 lines max) |

If a line doesn't fit any category, cut it.

---

## The Meta-Lesson

1. **Add knowledge the model lacks** -- checklists, non-obvious facts, gotchas
2. **Prevent mistakes the model makes** -- false positive checks, anti-patterns, constraints
3. **Stay out of the model's way** for everything else
