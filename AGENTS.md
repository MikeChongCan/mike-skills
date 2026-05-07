# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a personal Claude Code agent skills distribution repo, installable via:

```bash
npx skills add MikeChongCan/mike-skills
```

## Skill format

Each skill is a `SKILL.md` file with YAML frontmatter:

```markdown
---
name: skill-name
description: One-line trigger description (Claude reads this to decide when to invoke the skill)
---
```

The description field is the most important — it's what Claude uses to auto-invoke the skill. Make it specific and include common phrasings a user would say.

## Repo layout

Skills live under `skills/<name>/SKILL.md`. Add a new skill by creating a new subdirectory there.

Optional subdirectories per skill:
- `references/` — reference docs Claude may read during the skill
- `scripts/` — helper scripts the skill invokes
- `assets/` — static assets
- `evals/` — evaluation cases for the skill

## Conventions

- `CLAUDE.md` is a symlink to `AGENTS.md` — edit `AGENTS.md` directly, not `CLAUDE.md`.
- PNG assets committed to the repo should be optimized with `oxipng`/`pngquant` (the `png-optimize` skill handles this).
