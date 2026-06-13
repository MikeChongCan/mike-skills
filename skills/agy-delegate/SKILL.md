---
name: agy-delegate
description: >-
  Delegate implementation tasks to the antigravity (agy) coding agent as a local subagent.
  Use when: (1) User says "delegate to agy", "agy subagent", "run agy", "agy implement",
  (2) User invokes agy-strategy step 3 (delegate implementation to subagent),
  (3) A plan.md is ready and implementation should be handed off to a parallel coding agent,
  (4) Multiple non-conflicting modules (backend/frontend/mobile) need parallel implementation,
  (5) User wants to spawn one or more autonomous coding agents to execute a plan.
---

# agy-delegate

Delegate implementation work to `agy` — the antigravity multi-model coding agent CLI.

## When to use

- A `.plan.md` or spec is finalized and ready for implementation
- Work can be split into non-conflicting modules for parallel agents
- You want an autonomous agent that auto-approves tool calls and runs to completion

## Core command

```bash
agy --dangerously-skip-permissions --prompt "<task>"
```

### Key flags

| Flag | Purpose |
|---|---|
| `--dangerously-skip-permissions` | Auto-approve all tool calls (required for autonomous runs) |
| `--prompt "<task>"` / `-p "<task>"` | Run a single prompt non-interactively, print result, exit |
| `--prompt-interactive "<task>"` / `-i "<task>"` | Run initial prompt then continue the session interactively |
| `--model "<model>"` | Override model (see available models below) |
| `--add-dir <path>` | Add extra directory to workspace (repeatable) |
| `--sandbox` | Run in sandbox with terminal restrictions |

### Available models

```
Gemini 3.5 Flash (Medium|High|Low)
Gemini 3.1 Pro (Low|High)
Claude Sonnet 4.6 (Thinking)
Claude Opus 4.6 (Thinking)
GPT-OSS 120B (Medium)
```

Default to `Gemini 3.5 Flash (High)` — fast, capable, and cost-effective for most implementation tasks. Only escalate to `Claude Opus 4.6 (Thinking)` for exceptionally complex architectural work.

## Delegation patterns

### Single agent — plan-to-implementation

```bash
agy --dangerously-skip-permissions --prompt "Read apps/my-app/docs/feature.plan.md and fully implement it. Do not stop until every item is complete. Run tests after each module."
```

### Parallel agents — split by module

When work spans non-conflicting areas, spawn multiple `agy` processes via the Bash tool with `run_in_background: true`:

```bash
# Agent 1: backend
agy --dangerously-skip-permissions --prompt "Read docs/feature.plan.md and implement ONLY the backend changes in apps/service/. Run tests when done."

# Agent 2: mobile
agy --dangerously-skip-permissions --prompt "Read docs/feature.plan.md and implement ONLY the mobile changes in apps/mobile/. Do not touch apps/service/."
```

Split rules:
- Each agent works in a disjoint file set — no overlapping paths
- Each prompt explicitly scopes what to touch and what NOT to touch
- Write the plan so modules are independently implementable

### With model override

```bash
# Default — recommended for most tasks
agy --dangerously-skip-permissions --model "Gemini 3.5 Flash (High)" --prompt "Read the plan at docs/auth-refactor.plan.md and implement it completely."

# Heavy lifting — complex architecture only
agy --dangerously-skip-permissions --model "Claude Opus 4.6 (Thinking)" --prompt "Read the plan at docs/auth-refactor.plan.md and implement it completely."
```

### With extra workspace directories

```bash
agy --dangerously-skip-permissions --add-dir ../shared-types --prompt "Implement the API changes, referencing shared types in the added directory."
```

## Prompt engineering for agy

Write prompts that are self-contained — `agy` starts with zero conversation context.

### Effective prompt structure

1. **Point to the plan** — `"Read <path>.plan.md"`
2. **Scope the work** — `"implement ONLY the backend/mobile/frontend changes in <dir>"`
3. **Set completion criteria** — `"Run tests after each module"`, `"Do not stop until every item is complete"`
4. **Forbid out-of-scope changes** — `"Do not touch <other-dir>"`

### Example prompt template

```
Read <plan-path> and fully implement it.

Scope: ONLY files under <target-dir>/.
Do NOT modify files outside that directory.
After implementation, run the relevant test suite.
Do not stop until every item in the plan is complete.
Commit with a descriptive message when done.
```

## Workflow integration (agy-strategy)

This skill is step 3 of the `agy-strategy` workflow from CLAUDE.md:

1. Write `.plan.md` after exploring the codebase
2. Review loop (acpx + gemini/codex review the plan)
3. **Delegate to agy** (this skill) — spawn subagent(s) to implement
4. Self-review + acpx review loop on the implementation

## Running from Claude Code

Use the Bash tool with `run_in_background: true` and a generous timeout:

```bash
# Single agent
Bash({ command: 'agy --dangerously-skip-permissions --prompt "..."', run_in_background: true, timeout: 600000 })

# Parallel agents — send both in a single message
Bash({ command: 'agy --dangerously-skip-permissions --prompt "... backend ..."', run_in_background: true, timeout: 600000 })
Bash({ command: 'agy --dangerously-skip-permissions --prompt "... mobile ..."', run_in_background: true, timeout: 600000 })
```

Wait for background task notifications. Do not poll or sleep.
