# `/scaffold` — build-it-yourself prompt

Copy everything between the `---` lines below and paste it into Claude Code.
Claude will interactively ask which work blocks you typically have in your projects, then assemble a personalised `SKILL.md` at `~/.claude/skills/scaffold/SKILL.md`.

---

Hi Claude! Help me set up a global `/scaffold` skill on my machine — it will unfold a universal project-management-style new-project structure (client work, internal initiative, research, launch). Follow the steps below exactly.

### Step 1 — ask me via a checkbox picker which blocks I need

**YOU MUST use the `AskUserQuestion` tool with `multiSelect: true` (checkboxes). DO NOT ask in plain text.**

`AskUserQuestion` parameters:

- **question**: "Which work blocks do you typically have in your projects? Pick all that apply — can be several."
- **header**: "Project blocks"
- **multiSelect**: `true`
- **options** (label / description):
  - **"Client work"** — external client, deliverable, contract, communication
  - **"Internal initiative"** — internal project, product, R&D, operations
  - **"Research"** — discovery, interviews, analysis, synthesis
  - **"Launch / Campaign"** — event, release, marketing campaign with a deadline

(The "Other" field will appear automatically — I can add my own blocks there, comma-separated.)

### Step 2 — remember my selection

Save the list of blocks I picked (including anything I typed in Other). I'll refer to this list as "my blocks" below.

For each of the 4 standard blocks, use the fixed folder set (see the table in the template below).

For each custom block from "Other", use the **default set**: `<block-slug>/deliverables/` and `<block-slug>/notes/` subfolders inside the project.

### Step 3 — create the skill folder

Create the folder `~/.claude/skills/scaffold/`, if it doesn't already exist.

### Step 4 — assemble and save SKILL.md

Save the content below into `~/.claude/skills/scaffold/SKILL.md`, adapted to my blocks:

- In the section **"Step 2 — ask for project blocks via picker"**, in the `options` parameter keep **only my selected blocks** (plus any custom ones from Other). Do not keep blocks I did not pick.
- In the **blocks → folders table**, keep only the rows for my selected blocks (plus rows for my custom blocks with the default folder set).
- If I selected only one block — keep `multiSelect: true` in Step 2 of the SKILL.md anyway (in case I want to add more blocks later via Other).
- Keep the "first ask for project name → create `<project-name>/` → put everything inside" logic as is.
- Keep the "union of folders from all selected blocks with deduplication" logic as is.
- Keep the "always" additions (`context/`, `.project-journal/`, `STATE.md`, `CLAUDE.md`) as is.

Here is the template. The always-files content inside is wrapped in `~~~` (tilde fences) — valid markdown that doesn't conflict with the outer fence. **Keep it as is** — Claude Code handles `~~~` correctly.

```markdown
---
name: scaffold
description: Scaffolds a universal new-project structure in project-management style. Trigger — the /scaffold command or user's explicit request such as "create project structure", "scaffolding", "set up project layout". First asks for the project name in text, then asks for project blocks via AskUserQuestion with checkboxes (multiSelect — pick several). Creates a folder `<project-name>/` in the current directory and unfolds inside it the union of folders for the selected blocks. Shows the plan and waits for confirmation before creating.
---

# /scaffold

Scaffolds a new-project structure: creates a folder `<project-name>/` in the current directory and puts inside it the union of folders for the selected work blocks.

## When triggered

- User entered the `/scaffold` command.
- User explicitly asked: "create project structure", "set up scaffolding", "set up project layout".

## Algorithm

### Step 1 — ask for the project name (text)

Ask in text: "What shall we name the project? I'll create a folder with that name in the current directory and unfold the structure inside it." Wait for the answer.

If the answer contains spaces or uppercase — convert to kebab-case. Save the result as `<project-name>`.

### Step 2 — ask for project blocks via picker with checkboxes

**YOU MUST call the `AskUserQuestion` tool with `multiSelect: true`. DO NOT ask in plain text.**

`AskUserQuestion` parameters:

- **question**: "Which work blocks will be in the project? Pick all that apply — can be several."
- **header**: "Project blocks"
- **multiSelect**: `true`
- **options**: [ONLY the blocks the user selected when assembling this skill]

The "Other" field appears automatically — add custom blocks with the default set (`<slug>/deliverables/`, `<slug>/notes/`).

### Step 3 — assemble the plan

Union of folders across all selected blocks. Deduplicate repeats.

| Block | Folders (inside `<project-name>/`) |
|------|---|
| Client work | `brief/`, `research/`, `deliverables/`, `communication/` |
| Internal initiative | `docs/`, `deliverables/`, `roadmap/`, `meetings/` |
| Research | `interviews/`, `research/`, `findings/`, `synthesis/` |
| Launch / Campaign | `plan/`, `assets/`, `checklist/`, `comms/` |
| Other (custom block) | `<slug>/deliverables/`, `<slug>/notes/` |

**Always** added: `context/`, `.project-journal/`, `.project-journal/STATE.md`, `CLAUDE.md`.

### Step 4 — show the plan, ask for confirmation

List what will be created. Wait for "yes" / "ok". Do not apply silently.

### Step 5 — create everything

1. Create `<project-name>/` in the current directory.
2. Inside — all folders plus the two files (`STATE.md`, `CLAUDE.md`).

If a file or folder already exists — do not overwrite, ask separately.

### Step 6 — report

Show the tree and suggest: "`cd <project-name>/`, open `.project-journal/STATE.md` and describe the task in one sentence".

## Always-files content

### `.project-journal/STATE.md`

~~~
# Project state

**What:** [one line]
**Stage:** discovery / in progress / finalization / done
**Updated:** [date]

## Done
- …

## In progress
- …

## Next steps
- …
~~~

### `CLAUDE.md`

~~~
# Claude — project rules

**Context:** [one line]

## Important to remember
- …

## What NOT to do
- …
~~~

## What NOT to do

- **Never create the structure in the current folder.** Always first ask for a name → subfolder `<project-name>/` → everything inside. If your hand reaches to create `brief/` in the current dir — mistake.
- **Never ask about blocks in plain text.** Always `AskUserQuestion` with `multiSelect: true`. If you feel like typing "Pick blocks: 1) … 2) …" — mistake.
- Do not apply silently — show the plan and wait for confirmation.
- Do not overwrite existing files or folders.
- Do not create extra files inside folders "just in case" — leave folders empty except for `STATE.md` and `CLAUDE.md`. No templates inside blocks for now.
- Do not run side commands (git init, npm init).
- Do not duplicate folders — if they repeat across blocks, create once.
```

### Step 5 — confirm it's done

Show the path to the created `SKILL.md` and list the blocks that ended up in it.

### Step 6 — DO NOT run /scaffold

I'll try it myself in a fresh empty folder. Just create the skill and stop.

Thanks!
