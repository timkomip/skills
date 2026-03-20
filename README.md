# timkomip/skills

Personal Claude Code skills.

## Install

```bash
npx @anthropic-ai/skills install timkomip/skills commit
```

## Skills

| Skill | Description |
|-------|-------------|
| `commit` | Break local changes into logical commits, present for review, then execute |

## Recommended Skills

| Skill | Source | Description |
|-------|--------|-------------|
| `grill-me` | [mattpocock/skills](https://github.com/mattpocock/skills) | Interview relentlessly about a plan or design until reaching shared understanding |

## Adding Skills

Create `skills/<name>/SKILL.md` with frontmatter:

```yaml
---
name: my-skill
description: What it does and when to trigger it
---
```
