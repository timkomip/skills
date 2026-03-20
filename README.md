# timkomip/skills

Personal Claude Code skills marketplace.

## Setup

```bash
# Add this repo as a marketplace source:
claude plugin marketplace add timkomip/skills

# Install a skill:
claude plugin install grill-me@skills
```

## Update

```bash
claude plugin marketplace update skills
claude plugin update grill-me@skills
```

## Available Skills

| Skill | Description |
|-------|-------------|
| `grill-me` | Interview relentlessly about a plan or design until reaching shared understanding. Credit: [Matt Pocock](https://github.com/mattpocock/skills) |

## Adding Skills

Create a new dir under `plugins/` following the `grill-me` pattern:

```
plugins/<skill-name>/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── <skill-name>/
        └── SKILL.md
```
