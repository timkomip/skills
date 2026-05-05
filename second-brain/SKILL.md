---
name: second-brain
description: Capture and retrieve personal notes — tasks, lessons, ideas, info on people and projects — stored as dated markdown bullets in `~/notes/SecondBrain/`. Use when user says "save to my second brain", "remember that...", "remind me to look into...", "don't let me do X again", "what do I have on X", "second brain about Y", or otherwise wants to capture/recall personal knowledge without organizing it manually.
---

# Second Brain

Append to or search `~/notes/SecondBrain/`. Picks the right file from intent so the user doesn't organize.

## Vault layout

```
~/notes/SecondBrain/
├── tasks.md          # things to do, things to look into
├── lessons.md        # don't-do-this-again, learnings
├── ideas.md          # half-baked thoughts to revisit
├── notes.md          # general info / catch-all
├── people/<slug>.md  # per-person: who they are, questions, things they said
└── projects/<slug>.md # per-project: context, decisions, open threads
```

Create files/dirs on demand — don't error if missing, just `mkdir -p` and append.

Slug rule: lowercase, hyphenated (`sarah-chen`, `cnxt-app`).

## Write workflow

User intent → file:

| Phrasing | File |
|---|---|
| "remind me to / look into / I should X" | `tasks.md` |
| "don't do X again / lesson learned / I keep forgetting" | `lessons.md` |
| "thought / wondering / what if" | `ideas.md` |
| any mention of a specific person | `people/<slug>.md` |
| tied to a named project/codebase | `projects/<slug>.md` |
| anything else worth keeping | `notes.md` |

If ambiguous, pick the best fit and tell the user where you put it. Don't ask.

Append format — dated bullet:

```
- 2026-05-05 — look into xstate persistence for cnxt agent
```

For people/projects, use dated sub-headings if entries are longer than a bullet:

```
## 2026-05-05
- Sarah is leading the auth rewrite
- Asked about her preferred review style — answer: async, one PR at a time
```

Use today's date from the environment context (e.g. `2026-05-05`). Append to bottom of file. Don't reorder existing content.

After writing, tell the user the file path in one line. Done.

## Read workflow

User asks "what do I have on X" / "second brain about Y":

1. `grep -rni "<keywords>" ~/notes/SecondBrain/` — case-insensitive, recursive, with line numbers
2. If person/project mentioned by name, also `cat` the matching `people/<slug>.md` or `projects/<slug>.md` directly
3. Return matches with file paths so the user can jump to source. Group by file. Don't editorialize unless asked

If no matches, say so plainly — don't fabricate.

## Notes

- Do not modify entries the user wrote (only append). Editing is a separate, explicit ask.
- Existing files in `~/notes/` (e.g. `~/notes/tasks.md`) are NOT part of the second brain. Don't read or write to those unless asked.
- No frontmatter, no YAML in entries — just bullets and dates. Plain markdown stays grep-friendly.
