# mike-skills

Mike's personal collection of [Claude Code](https://claude.ai/code) agent skills.

## Install

```bash
npx skills add rockiestar/mike-skills
```

Or install a specific skill by name:

```bash
npx skills add rockiestar/mike-skills --skill png-optimize
```

Skills are installed into `.claude/skills/` in your project (or `~/.claude/skills/` globally).

## Skills

### `png-optimize`

Optimize and minimize PNG files for smaller file sizes using `oxipng` and `pngquant`. Two-pass approach: lossy quantization followed by lossless compression.

**Triggers on:** "optimize PNGs", "compress images", "reduce image size", "minimize assets", "oxipng", "pngquant", "optipng"

**Requires:** `oxipng` and/or `pngquant` (installs via Homebrew if missing)

## Structure

```
SKILL.md          # Active skill (root = single-skill install)
AGENTS.md         # Repo instructions for AI agents (CLAUDE.md symlinks here)
CLAUDE.md         # Symlink → AGENTS.md
README.md
```

## Adding more skills

To distribute multiple skills from one repo, move each into a named subdirectory:

```
skills/
  png-optimize/
    SKILL.md
  another-skill/
    SKILL.md
```

`npx skills` discovers skills at root or under `skills/`.

## References

- [Claude Code skill docs](https://code.claude.ai/docs/en/skills)
- [Agent Skills standard — agentskills.io](https://agentskills.io)
- [npx skills CLI — vercel-labs/skills](https://github.com/vercel-labs/skills)
