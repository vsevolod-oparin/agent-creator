# Agent Creator

A Claude Code plugin for creating, evaluating, and improving agent descriptions (`.claude/agents/*.md` files).

## What it does

Agent descriptions are the instructions that shape how Claude Code agents behave on specialized tasks -- code review, security audits, implementation, research. A well-written agent description injects domain knowledge the model lacks and prevents mistakes it naturally makes. A poorly-written one actively degrades output quality below the bare model.

This plugin gives you two tools:

- **agent-creator** (skill): The full workflow for creating new agents or improving existing ones. Walks you through intent capture, research, writing, testing, and iterative evaluation. Scores agent descriptions on 6 dimensions and measures the actual lift over bare-model performance.

- **/audit-agent** (command): Quick quality audit of existing agent descriptions. Scores each agent against a quality rubric, flags anti-patterns, and suggests specific improvements. No test runs needed -- just reads and analyzes.

## Installation

**Local development** (load for a single session):
```bash
claude --plugin-dir /path/to/agent-creator
```

**From a GitHub repo** (permanent):
```bash
# 1. Add the marketplace (one-time)
/plugin marketplace add owner/agent-creator

# 2. Install the plugin
/plugin install agent-creator@agent-creator-marketplace
```

**From a local directory** (permanent):
```bash
# 1. Add as local marketplace (one-time)
/plugin marketplace add /path/to/agent-creator

# 2. Install the plugin
/plugin install agent-creator@agent-creator-marketplace
```

## Usage

Once loaded, the skill and command are namespaced:
- Skill: triggered automatically when you ask to create or improve an agent
- Command: `/agent-creator:audit-agent`

## What's inside

```
skills/agent-creator/
  SKILL.md              Main skill -- the creation/improvement workflow
  agents/
    grader.md           Scores agent output against ground truth
    comparator.md       Blind A/B comparison of agent versions
  references/
    design-guide.md     Condensed agent description design guide
    quality-rubric.md   Scoring rubric for agent descriptions
    anti-patterns.md    What kills agent quality (with evidence)
    schemas.md          JSON schemas for eval files

commands/
  audit-agent.md        Quick audit command for existing agents
```

## Key principles

1. **Add knowledge the model lacks** -- domain checklists, non-obvious facts, version-specific gotchas
2. **Prevent mistakes the model makes** -- false positive checks, anti-patterns, behavioral constraints
3. **Stay out of the model's way** for everything else -- no process instructions, no rigid templates, no generic advice

The model is already good. Your job is to make it accurate on the edges, not to teach it fundamentals.
