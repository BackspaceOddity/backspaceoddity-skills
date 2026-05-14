---
name: scaffold
description: Scaffolds a universal new-project structure in project-management style. Trigger — the /scaffold command or user's explicit request such as "create project structure", "scaffolding", "set up project layout". First asks for the project name in text, then asks for project blocks via AskUserQuestion with checkboxes (multiSelect — pick several). Creates a folder `<project-name>/` in the current directory and unfolds inside it the union of folders for the selected blocks (client work, internal initiative, research, launch). Shows the plan and waits for confirmation before creating.
---

# /scaffold

Scaffolds a new-project structure: creates a folder `<project-name>/` in the current directory and puts inside it the union of folders for the selected work blocks (client / internal / research / launch).

## When triggered

- User entered the `/scaffold` command.
- User explicitly asked: "create project structure", "set up scaffolding", "set up project layout".

## Algorithm

### Step 1 — ask for the project name (text)

Ask in text: "What shall we name the project? I'll create a folder with that name in the current directory and unfold the structure inside it." Wait for the answer.

If the answer contains spaces or uppercase — convert to kebab-case (lowercase, spaces → dashes, strip special characters). Save the result as `<project-name>`.

### Step 2 — ask for project blocks via picker with checkboxes

**YOU MUST use the `AskUserQuestion` tool with `multiSelect: true` (checkboxes, can pick several). DO NOT ask the question in text. DO NOT prompt the user to enter letters or numbers.**

`AskUserQuestion` parameters:

- **question**: "Which work blocks will be in the project? Pick all that apply — can be several."
- **header**: "Project blocks"
- **multiSelect**: `true`
- **options** (label / description):
  - **"Client work"** — external client, deliverable, contract, communication
  - **"Internal initiative"** — internal project, product, R&D, operations
  - **"Research"** — discovery, interviews, analysis, synthesis
  - **"Launch / Campaign"** — event, release, marketing campaign with a deadline

The "Other" field appears automatically — if the user enters their own blocks (comma-separated), add them with the default folder set (see table below).

### Step 3 — assemble the plan

Take the **union** of folders from all selected blocks. If a folder repeats across blocks — create it **once** (deduplicate).

**All folders are created inside `<project-name>/`**, not in the current directory.

| Block | Folders (inside `<project-name>/`) |
|------|---|
| Client work | `brief/`, `research/`, `deliverables/`, `communication/` |
| Internal initiative | `docs/`, `deliverables/`, `roadmap/`, `meetings/` |
| Research | `interviews/`, `research/`, `findings/`, `synthesis/` |
| Launch / Campaign | `plan/`, `assets/`, `checklist/`, `comms/` |
| **Other (custom block)** | `<block-slug>/deliverables/`, `<block-slug>/notes/` |

**Always added** (regardless of selection):
- Folders: `context/`, `.project-journal/`
- Files: `.project-journal/STATE.md`, `CLAUDE.md`

### Step 4 — show the plan, ask for confirmation

Before creating anything, list out: the `<project-name>/` folder and inside it all folders from the union of the selected blocks plus the "always" additions (`context/`, `.project-journal/`, `STATE.md`, `CLAUDE.md`). Wait for "yes" / "ok". Do not apply silently.

### Step 5 — create everything

1. Create `<project-name>/` in the current directory. If such a folder already exists — DO NOT overwrite. Ask separately.
2. Inside `<project-name>/`, create all folders plus the two files (`STATE.md`, `CLAUDE.md`) from the plan.

If a specific file already exists — DO NOT overwrite. Ask separately for each conflict.

### Step 6 — report

Show a tree of the created structure and suggest the next step in one line — e.g.: "`cd <project-name>/`, open `.project-journal/STATE.md` and describe the task in one sentence".

## Always-files content

### `.project-journal/STATE.md`

```markdown
# Project state

**What:** [one line — what is this project]
**Who:** [user / team / client]
**Stage:** discovery / in progress / finalization / done
**Updated:** [date]

## Done
- [date]: …

## In progress
- …

## Next steps
- …
```

### `CLAUDE.md`

```markdown
# Claude — project rules

**Context:** [one line about the project]

## Important to remember
- …

## What NOT to do
- …
```

## What NOT to do

- **Never create the structure in the current folder.** Always first ask for a name and create the subfolder `<project-name>/`, then put `brief/`, `docs/`, etc. inside it. If your hand reaches to create `brief/` in the current dir — that's a mistake, double-check yourself.
- **Never ask about blocks in plain text.** Always call `AskUserQuestion` with `multiSelect: true`. If you feel like typing "Pick blocks: 1) … 2) …" — that's a mistake, double-check and call the tool.
- Do not apply silently — show the plan and wait for confirmation.
- Do not overwrite existing files or folders — ask separately for each conflict.
- Do not create extra files inside folders "just in case" — leave folders empty except for `STATE.md` and `CLAUDE.md`. No templates inside blocks for now.
- Do not run side commands (`git init`, `npm init`, etc.) — that's outside the scope.
- Do not duplicate folders — if they repeat across blocks, create once.
