---
name: jira
description: Jira Cloud via Atlassian CLI (acli). Use when users need to create, edit, search, transition, or manage Jira work items (epics, stories, tasks, bugs), sprints, or boards. Triggers include "jira", "ticket", "issue", "epic", "story", "sprint", "backlog", "create a ticket", "move to in progress", "assign to me".
---

# Jira Cloud via Atlassian CLI (acli)

Manage Jira Cloud work items using the `acli` CLI.

## Dependency Check

**Before executing any `acli` command**, verify the CLI is installed:

```bash
acli --version
```

If not found, see [Atlassian CLI install docs](https://developer.atlassian.com/cloud/acli/).

## Authentication

```bash
acli jira auth login
acli jira auth status
```

## Key Concepts

- **Work items** = issues (epics, stories, tasks, bugs, subtasks)
- **Parent** = epic or parent issue (use `--parent KEY`)
- **Transitions** = status changes (e.g., "To Do" → "In Progress" → "Done")

## Rich Descriptions with ADF

Jira Cloud uses **Atlassian Document Format (ADF)** for rich text. The `--description` and `--description-file` flags accept either plain text or ADF JSON.

**Always use ADF** for descriptions that need formatting (headings, bold, lists, code). Plain text renders without any formatting.

### ADF Inline Example

```bash
acli jira workitem create \
  --project "PROJ" \
  --type "Story" \
  --summary "My story" \
  --description '{"version":1,"type":"doc","content":[{"type":"heading","attrs":{"level":2},"content":[{"type":"text","text":"Overview"}]},{"type":"paragraph","content":[{"type":"text","text":"Description here."}]}]}'
```

### ADF Reference

ADF is a JSON document with `version`, `type`, and `content` array. Common node types:

| Node | Usage |
|------|-------|
| `paragraph` | `{"type":"paragraph","content":[{"type":"text","text":"..."}]}` |
| `heading` | `{"type":"heading","attrs":{"level":2},"content":[{"type":"text","text":"..."}]}` |
| `bulletList` | `{"type":"bulletList","content":[...listItems]}` |
| `orderedList` | `{"type":"orderedList","attrs":{"order":1},"content":[...listItems]}` |
| `listItem` | `{"type":"listItem","content":[...paragraphs]}` |
| `codeBlock` | `{"type":"codeBlock","attrs":{"language":"bash"},"content":[{"type":"text","text":"..."}]}` |

Text marks for inline formatting:

| Mark | Usage |
|------|-------|
| **Bold** | `{"type":"text","text":"bold","marks":[{"type":"strong"}]}` |
| *Italic* | `{"type":"text","text":"italic","marks":[{"type":"em"}]}` |
| `Code` | `{"type":"text","text":"code","marks":[{"type":"code"}]}` |

### ADF from File

For large descriptions, write ADF JSON to a temp file:

```bash
acli jira workitem create \
  --project "PROJ" \
  --type "Story" \
  --summary "My story" \
  --description-file "/tmp/description.json"
```

## Quick Command Reference

| Task | Command |
|------|---------|
| Create work item | `acli jira workitem create --project "PROJ" --type "Story" --summary "..."` |
| Create with parent | `acli jira workitem create --project "PROJ" --type "Story" --parent "PROJ-1" --summary "..."` |
| View work item | `acli jira workitem view --key "PROJ-1"` |
| Edit work item | `acli jira workitem edit --key "PROJ-1" --summary "New title"` |
| Transition status | `acli jira workitem transition --key "PROJ-1" --status "In Progress" --yes` |
| Assign | `acli jira workitem assign --key "PROJ-1" --assignee "user@example.com"` |
| Self-assign | `acli jira workitem assign --key "PROJ-1" --assignee "@me"` |
| Search (JQL) | `acli jira workitem search --jql "project = PROJ AND status = 'In Progress'"` |
| Add comment | `acli jira workitem comment add --key "PROJ-1" --body "comment text"` |
| Link issues | `acli jira workitem link add --key "PROJ-1" --target "PROJ-2" --type "blocks"` |
| Delete | `acli jira workitem delete --key "PROJ-1" --yes` |
| List projects | `acli jira project list --recent` |
| List sprints | `acli jira sprint list --board <id>` |
| List boards | `acli jira board list --recent` |

## Work Item CRUD

### Create

```bash
# Basic
acli jira workitem create \
  --project "PROJ" \
  --type "Story" \
  --summary "Add user authentication" \
  --assignee "user@example.com"

# Epic
acli jira workitem create \
  --project "PROJ" \
  --type "Epic" \
  --summary "Authentication overhaul" \
  --assignee "user@example.com" \
  --description '{"version":1,"type":"doc","content":[{"type":"paragraph","content":[{"type":"text","text":"Epic description here."}]}]}'

# Story under an epic
acli jira workitem create \
  --project "PROJ" \
  --type "Story" \
  --parent "PROJ-100" \
  --summary "Implement JWT auth" \
  --assignee "user@example.com" \
  --label "auth,backend"

# From JSON (for complex work items)
acli jira workitem create --from-json "workitem.json"

# Generate a JSON template
acli jira workitem create --generate-json
```

### View

```bash
acli jira workitem view --key "PROJ-1"
acli jira workitem view --key "PROJ-1" --json
```

### Edit

```bash
acli jira workitem edit --key "PROJ-1" --summary "Updated title" --yes
acli jira workitem edit --key "PROJ-1" --description-file "/tmp/desc.json" --yes
acli jira workitem edit --key "PROJ-1" --labels "new-label" --yes
acli jira workitem edit --key "PROJ-1" --assignee "other@example.com" --yes

# Bulk edit via JQL
acli jira workitem edit --jql "project = PROJ AND status = 'To Do'" --assignee "user@example.com" --yes
```

### Transition

```bash
acli jira workitem transition --key "PROJ-1" --status "In Progress" --yes
acli jira workitem transition --key "PROJ-1" --status "Done" --yes

# Bulk transition
acli jira workitem transition --key "PROJ-1,PROJ-2,PROJ-3" --status "Done" --yes
```

### Search

```bash
# By JQL
acli jira workitem search --jql "project = PROJ AND assignee = currentUser() AND status != Done"
acli jira workitem search --jql "project = PROJ AND type = Epic" --json

# By filter
acli jira workitem search --filter 10001
```

### Comments

```bash
acli jira workitem comment add --key "PROJ-1" --body "This is done"
acli jira workitem comment list --key "PROJ-1"
```

### Links

```bash
acli jira workitem link add --key "PROJ-1" --target "PROJ-2" --type "relates to"
acli jira workitem link add --key "PROJ-1" --target "PROJ-2" --type "blocks"
```

### Attachments

```bash
acli jira workitem attachment add --key "PROJ-1" --file "screenshot.png"
acli jira workitem attachment list --key "PROJ-1"
```

## Sprints & Boards

```bash
acli jira board list --recent
acli jira sprint list --board <board-id>
acli jira sprint create --board <board-id> --name "Sprint 5" --start "2025-01-01" --end "2025-01-14"
```

## Common Flags

| Flag | Description |
|------|-------------|
| `--project "KEY"` | Project key |
| `--type "Story"` | Work item type: Epic, Story, Task, Bug, Subtask |
| `--parent "KEY-1"` | Parent work item (for child stories under epics) |
| `--assignee "email"` | Assignee email, `@me`, or `default` |
| `--label "a,b"` | Comma-separated labels |
| `--json` | JSON output |
| `--yes` / `-y` | Skip confirmation prompts |
| `--key "KEY-1"` | Target work item key(s), comma-separated for bulk |
| `--jql "..."` | JQL query for bulk operations |

## Tips

- Use `--json` output and pipe to `jq` for scripting
- Use `--yes` to skip confirmation prompts in automation
- The `--key` flag accepts comma-separated keys for bulk operations
- Use `--generate-json` on create/edit to see the full JSON schema
- JQL reference: [Atlassian JQL docs](https://support.atlassian.com/jira-software-cloud/docs/use-advanced-search-with-jira-query-language-jql/)
