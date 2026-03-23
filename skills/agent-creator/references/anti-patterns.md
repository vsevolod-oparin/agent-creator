# Anti-Patterns in Agent Descriptions

Every anti-pattern discovered through empirical testing, with explanations of why each degrades agent quality.

---

## 1. Rigid Output Templates

**What:** Mandating exact output formats -- severity tables, finding templates, section structures.

**Why it hurts:** The model spends tokens on format compliance instead of analysis. Output gets compressed significantly, and both Depth and Completeness drop. Rigid templates consistently cause one of the largest quality regressions measured.

**Fix:** Suggest output structure, don't mandate it. "Report findings with severity, file location, and fix suggestion" works. A 15-line template doesn't.

---

## 2. "Be Thorough" Instructions

**What:** Explicit instructions to be thorough, comprehensive, systematic, rigorous.

**Why it hurts:** It doesn't hurt -- it just wastes space. The model is already proactive. The instruction is redundant noise competing with useful content for attention. Removing these instructions consistently shows zero regression.

**Fix:** Delete these lines. Use the space for a domain checklist item instead.

---

## 3. Verification Gates That Cause Self-Censoring

**What:** Requirements to "prove" findings before reporting them (e.g., "only report vulnerabilities you can prove are reachable").

**Why it hurts:** Binary gates (report / don't report) force the model to choose between thoroughness and confidence. It consistently chooses confidence, suppressing findings it can't fully prove. The most dangerous vulnerabilities are exactly the ones hardest to prove without runtime access -- race conditions, authorization bugs, and similar classes get suppressed first.

**Fix:** Use graduated confidence tiers: CONFIRMED / LIKELY / POSSIBLE. Report all tiers. Let the reader triage.

---

## 4. Process Instructions and Workflows

**What:** Step-by-step process sections telling the model how to work (read files, analyze, write findings, verify, format).

**Why it hurts:** Process instructions consume context budget without adding domain knowledge. The model already knows how to read files, analyze code, and write findings. Telling it how to work is attention tax -- it reduces the budget available for actually doing the work well. Adding process sections consistently causes regressions.

**Exception:** Constraints on process that prevent known failures are fine: "Write the test. Run it. Confirm FAILS. Only then implement" is a behavioral constraint, not a generic workflow.

**Fix:** Cut all process sections. Replace with domain checklists if you need content.

---

## 5. Adjective Lists as Instructions

**What:** Listing qualities you want the output to have -- "systematic", "thorough", "comprehensive", "actionable", "rigorous".

**Why it hurts:** Adjectives describe qualities but don't teach capabilities. "Be comprehensive" doesn't tell the model what to comprehensively check for. Domain checklists do. Agents consisting primarily of adjective lists consistently show zero lift over the bare model.

**Fix:** Replace every adjective with a concrete instruction. "Thorough" becomes a checklist of specific items to check. "Actionable" becomes "include the exact fix, not just the diagnosis."

---

## 6. Batch Changes Across Multiple Agents

**What:** Changing multiple agents simultaneously based on a theory of what should work.

**Why it hurts:** Simultaneous changes make it impossible to identify what helped and what hurt. Individual improvements can mask each other's regressions. The interaction effects between changes are unpredictable. Empirically, incremental single-agent updates improve quality while batch updates to many agents regress it -- even when applying the same design principles.

**Fix:** Update one agent at a time. Evaluate. Confirm improvement. Then move to the next.

---

## 7. Cutting Domain Checklists

**What:** Removing domain-specific checklists to reduce agent length.

**Why it hurts:** Domain checklists are knowledge injection. They remind the model of specific failure patterns it would otherwise skip. For review agents, the checklists ARE the product. Cutting checklists consistently produces the single largest regression of any prompt change, and restoring them recovers the full loss.

**Fix:** When cutting for length, cut process sections and adjective lists first. Checklists are the last thing to remove.

---

## 8. Stale Domain Facts

**What:** Including domain-specific guidance that was correct at training time but is now wrong.

**Example:** Recommending "never use synchronized around I/O in virtual threads" -- correct for JDK 21-23 but wrong for JDK 24+ where synchronized pinning was eliminated. The agent would actively teach incorrect guidance.

**Why it hurts:** The model trusts its instructions over its training data. A stale fact in the agent description will override the model's correct knowledge, producing confidently wrong output.

**Fix:** Pin version numbers to claims. "In JDK 21-23, avoid synchronized in virtual thread code paths (pinning risk)." Audit domain facts periodically against current documentation.

---

## 9. Trigger Conditions and Success Metrics

**What:** Sections defining when the agent should activate and what success looks like.

**Why it hurts:** The agent is already triggered -- it doesn't need to know when to activate. Completion criteria and success metrics are for humans managing processes, not for models doing work. These sections consistently show zero benefit and waste 15-25 lines of context budget.

**Fix:** Delete them entirely.

---

## 10. Generic Best Practices the Model Already Knows

**What:** Including standard best practices that any capable model would follow without prompting.

**Why it hurts:** Generic instructions compete with your task-specific content for the model's attention. Every line of "use parameterized queries" is a line that could have been a domain-specific gotcha the model actually needs. Agents consisting primarily of generic best practices show negligible lift over the bare model.

**Test:** "Would the bare model get this wrong?" If no, cut it.

---

## Summary: Impact Rankings

| Anti-Pattern | Impact Level |
|-------------|--------|
| Cutting domain checklists | Severe regression |
| Rigid output templates | Significant regression |
| Batch changes | Significant regression |
| Verification gates (self-censoring) | Moderate regression |
| Generic knowledge | Negligible lift (wasted potential) |
| "Be thorough" | Zero effect |
| Adjective lists | Zero effect |
| Trigger/completion criteria | Zero effect |
| Stale domain facts | Variable (can be severe) |
| Process instructions | Variable (consistent regressions) |
