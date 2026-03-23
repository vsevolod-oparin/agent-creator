# JSON Schemas

Schemas for agent-creator evaluation files.

---

## evals.yaml

Defines test tasks with ground truth for an agent. Located at `evals/evals.yaml` within the workspace.

```yaml
agent_name: code-reviewer
tasks:
  - task_id: review-react-hooks
    input: |
      Review this React component for issues.
      [code or file reference]
    ground_truth:
      must_mention:
        - missing dependency array in useEffect
        - key prop using array index in reordering list
      must_not:
        - flagging SHA256 for checksum use
        - suggesting PropTypes in TypeScript project
      structure:
        - findings grouped by severity
        - file locations included with line numbers
    files: []

  - task_id: review-api-auth
    input: |
      Review this Express API for security issues.
      [code or file reference]
    ground_truth:
      must_mention:
        - missing rate limiting on auth endpoints
        - JWT secret from environment variable not validated
      must_not:
        - flagging bcrypt rounds under 15 (12 is standard)
      structure:
        - security findings separate from code quality
    files:
      - evals/files/api-router.ts
```

**Fields:**
- `agent_name`: Name matching the agent's filename (without .md)
- `tasks[].task_id`: Unique string identifier
- `tasks[].input`: The task prompt to execute
- `tasks[].ground_truth.must_mention`: Items the agent must find (tests Completeness)
- `tasks[].ground_truth.must_not`: False positives the agent must avoid (tests Precision)
- `tasks[].ground_truth.structure`: Output organization expectations (tests Structure)
- `tasks[].files`: Optional input files (paths relative to workspace)

---

## grading.json

Output from the grader agent. Located at `<run-dir>/grading.json`.

```json
{
  "task_id": "review-react-hooks",
  "scores": {
    "completeness": {
      "score": 4,
      "justification": "Found 3/4 must_mention items. Missed the useCallback dependency issue."
    },
    "precision": {
      "score": 5,
      "justification": "Zero false positives. Correctly ignored SHA256 checksum usage."
    },
    "actionability": {
      "score": 4,
      "justification": "Most findings include fix suggestions. Two findings describe the problem without a fix."
    },
    "structure": {
      "score": 4,
      "justification": "Findings grouped by severity. File locations present. Missing line numbers on 2 findings."
    },
    "efficiency": {
      "score": 3,
      "justification": "Good signal but verbose. Several findings could be more concise."
    },
    "depth": {
      "score": 5,
      "justification": "Root cause analysis on the key findings. Traced the useEffect issue to its performance impact."
    }
  },
  "composite": 4.27,
  "must_mention_results": [
    {"item": "missing dependency array in useEffect", "found": true, "evidence": "Finding #2 identifies this exactly"},
    {"item": "key prop using array index", "found": true, "evidence": "Finding #5 flags array index keys"},
    {"item": "useCallback dependency issue", "found": false, "evidence": null}
  ],
  "must_not_results": [
    {"item": "flagging SHA256 for checksum use", "violated": false},
    {"item": "suggesting PropTypes in TypeScript project", "violated": false}
  ],
  "notes": "Strong precision. Completeness gap on useCallback is the main improvement target."
}
```

**Fields:**
- `task_id`: Matches the eval task
- `scores`: Per-dimension scores (1-5) with one-line justification
- `composite`: Weighted composite: (Precision x 2 + Completeness x 1.5 + rest x 1) / 7.5
- `must_mention_results`: Per-item pass/fail with evidence
- `must_not_results`: Per-item violation check
- `notes`: Summary observation for iteration guidance

---

## benchmark.json

Aggregate comparison between agent and baseline. Located at `benchmarks/<timestamp>/benchmark.json`.

```json
{
  "metadata": {
    "agent_name": "code-reviewer",
    "agent_path": ".claude/agents/code-reviewer.md",
    "timestamp": "2026-03-23T14:30:00Z",
    "tasks_run": ["review-react-hooks", "review-api-auth", "review-perf"],
    "model": "claude-sonnet-4-20250514"
  },
  "results": {
    "with_agent": {
      "composite": {"mean": 4.27, "stddev": 0.31},
      "completeness": {"mean": 4.0, "stddev": 0.5},
      "precision": {"mean": 4.8, "stddev": 0.2},
      "actionability": {"mean": 4.2, "stddev": 0.4},
      "structure": {"mean": 4.0, "stddev": 0.3},
      "efficiency": {"mean": 3.5, "stddev": 0.5},
      "depth": {"mean": 4.5, "stddev": 0.3}
    },
    "bare": {
      "composite": {"mean": 3.45, "stddev": 0.42},
      "completeness": {"mean": 3.2, "stddev": 0.6},
      "precision": {"mean": 3.5, "stddev": 0.8},
      "actionability": {"mean": 3.8, "stddev": 0.3},
      "structure": {"mean": 3.5, "stddev": 0.4},
      "efficiency": {"mean": 3.2, "stddev": 0.5},
      "depth": {"mean": 3.5, "stddev": 0.6}
    }
  },
  "lift": {
    "composite": 0.82,
    "completeness": 0.80,
    "precision": 1.30,
    "actionability": 0.40,
    "structure": 0.50,
    "efficiency": 0.30,
    "depth": 1.00
  },
  "per_task": [
    {
      "task_id": "review-react-hooks",
      "with_agent": 4.53,
      "bare": 3.20,
      "lift": 1.33
    }
  ],
  "assessment": "Strong agent. Precision lift of 1.30 indicates anti-patterns list is working. Completeness lift of 0.80 from domain checklists. Efficiency is the weak spot -- consider cutting filler."
}
```

**Fields:**
- `metadata`: Agent info, timestamp, tasks run, model used
- `results`: Per-configuration means and stddevs for each dimension
- `lift`: Delta (with_agent - bare) for each dimension and composite
- `per_task`: Per-task composite scores for drill-down
- `assessment`: Summary observation for iteration guidance

---

## comparison.json

Output from blind A/B comparator. Located at `<workspace>/comparison.json`.

```json
{
  "winner": "A",
  "reasoning": "Version A catches the N+1 query issue that B misses, and correctly ignores the SHA256 false positive that B flags.",
  "scores": {
    "A": {
      "completeness": 4,
      "precision": 5,
      "actionability": 4,
      "structure": 4,
      "efficiency": 3,
      "depth": 5,
      "composite": 4.27
    },
    "B": {
      "completeness": 3,
      "precision": 3,
      "actionability": 4,
      "structure": 4,
      "efficiency": 4,
      "depth": 3,
      "composite": 3.40
    }
  },
  "dimension_winners": {
    "completeness": "A",
    "precision": "A",
    "actionability": "TIE",
    "structure": "TIE",
    "efficiency": "B",
    "depth": "A"
  },
  "specific_differences": [
    "A catches the N+1 query pattern that B misses entirely",
    "B incorrectly flags SHA256 checksum usage as a security issue",
    "B is more concise but at the cost of missing findings",
    "A provides root cause analysis where B stays surface-level"
  ]
}
```

**Fields:**
- `winner`: "A", "B", or "TIE"
- `reasoning`: Why the winner was chosen
- `scores`: Per-dimension scores for both versions
- `dimension_winners`: Which version wins each dimension
- `specific_differences`: Concrete observations about what differs
