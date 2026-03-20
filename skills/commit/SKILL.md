---
name: commit
description: Break local changes into logical commits, present for review, then commit. Use when user says "commit", "break into commits", or wants to commit staged/unstaged changes intelligently.
---

# Commit Skill

Break local changes into logical, well-scoped commits and execute them after user approval.

## User Hints

The user may provide additional context alongside the `/commit` invocation. Use these as guidance when building the plan:

- **File scope** — e.g., "only commit src/auth.ts and src/routes.ts". Ignore other changes.
- **Intent/theme** — e.g., "these are all related to the auth refactor". Use this to shape commit messages and grouping.
- **Specific instructions** — e.g., "split the CSS changes into their own commit". Override default grouping logic.

If no hints are provided, fall back to the default behavior below. Either way, the user will review and approve before anything is committed.

## Steps

### 1. Gather Context

Run these in parallel:
- `git diff` — unstaged changes
- `git diff --staged` — staged changes
- `git status` — untracked files
- `git log --oneline -20` — recent commit history (to detect message style)

If there are no changes, tell the user and stop.

### 2. Detect Commit Message Style

Analyze the last 20 commit messages to detect two things:

**Format conventions** (adopt if detected):
- **Conventional commits** (`feat:`, `fix:`, `chore:`, etc.) — match this prefix style
- **Ticket references** (e.g., `PROJ-123`, `#456`) — if the repo includes ticket IDs, ask the user for the relevant ticket ID(s) once before presenting the plan

**Message quality** (always enforce regardless of repo history):
- Do NOT mimic low-effort messages like "fix bug", "update", "wip" even if that's what the repo history looks like
- Every commit message must be concise but informative — someone reading `git log --oneline` should understand *what* changed without opening the diff
- Subject line: imperative mood, under 72 chars, specific (e.g., "fix off-by-one in cursor pagination" not "fix bug")
- Body (optional but encouraged for non-trivial changes): 1-3 lines explaining *why* or giving context. Separate from subject with a blank line.

If no format convention is detected, use plain imperative mood (e.g., "add user auth endpoint").

### 3. Break Changes into Logical Commits

Group changes into the smallest logical units that each make sense independently. Consider:
- Changes to the same feature/module go together
- Test changes go with the code they test
- Config/tooling changes are separate from feature work
- Renames/moves are separate from behavior changes
- Unrelated bug fixes are separate commits

For each proposed commit, note:
- Which files (or hunks within files) are included
- The commit message

### 4. Present the Plan

Show each commit as a numbered section with breathing room between them:

```
══════════════════════════════════════
Commit 1
══════════════════════════════════════

Message:
────────
add user auth endpoint

JWT-based auth with refresh token support for the /api/auth routes.

Changes:
────────
  src/
  ├── auth.ts        (new)
  └── routes.ts      (modified)

∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿∿


══════════════════════════════════════
Commit 2
══════════════════════════════════════

Message:
────────
fix off-by-one in cursor pagination

Cursor was skipping the first result when offset > 0.

Changes:
────────
  src/
  └── paginator.ts   (modified)
```

For each commit show:
- **Subject line** — the commit message
- **Body** — 1-3 lines of context (omit if trivially obvious)
- **File tree** — grouped by directory, each file annotated with `(new)`, `(modified)`, `(deleted)`, or `(renamed)`

Then ask:
> Review the commits above. You can:
> - **approve** — commit all as shown
> - **edit** — change a message (e.g., "change 2 to: fix pagination offset")
> - **merge** — combine commits (e.g., "merge 1 and 2")
> - **split** — break a commit further (e.g., "split 3")
> - **drop** — remove a commit from the plan (changes stay unstaged)

### 5. Execute

Once approved:
1. For each commit in order, stage only the relevant files and create the commit
2. Use `git add -p` logic when a file has changes belonging to multiple commits — stage only the relevant hunks via a temporary patch approach
3. After all commits, run `git log --oneline -n <count>` to show the result

## Rules

- **Concise messages**: keep under 72 chars. Sacrifice grammar for brevity. Focus on *what* changed, not *why* (save that for the body if needed).
- **No empty commits**: if a proposed group has no actual diff, skip it.
- **Don't touch unrelated files**: only commit what's in the current diff/status.
- **Preserve staged state intent**: if the user already staged specific files, respect that grouping as a hint.
- **Co-author line**: append `Co-Authored-By: Claude <noreply@anthropic.com>` to each commit.
