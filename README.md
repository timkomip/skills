# timkomip/skills

Personal Claude Code skills.

## Install

```bash
npx skills@latest add timkomip/skills/commit
npx skills@latest add timkomip/skills/jira
npx skills@latest add timkomip/skills/second-brain
```

## Skills

| Skill | Description |
|-------|-------------|
| `commit` | Break local changes into logical commits, present for review, then execute |
| `jira` | Jira Cloud via Atlassian CLI (acli) — create, edit, search, transition work items |
| `second-brain` | Capture and retrieve personal notes (tasks, lessons, ideas, people, projects) in `~/notes/SecondBrain/` |

## Recommended Skills

| Skill | Source | Description |
|-------|--------|-------------|
| `grill-me` | [mattpocock/skills](https://github.com/mattpocock/skills) | Interview relentlessly about a plan or design until reaching shared understanding |

## Adding Skills

Create `<name>/SKILL.md` with frontmatter:

```yaml
---
name: my-skill
description: What it does and when to trigger it
---
```
