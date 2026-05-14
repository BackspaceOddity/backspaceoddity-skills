---
name: project-journal
description: >
  Automatically maintain a living project journal for every active project. This skill MUST trigger at the END of every Claude Code session where significant work was done — files created or edited, decisions made, strategies tried, errors encountered, or deliverables produced. Also trigger when the user says "save state", "update journal", "обнови журнал", "сохрани состояние", "save progress", "зафиксируй", "what's the status", "какой статус", or asks to prepare a handoff for another session. Use this skill for ANY project, not just specific ones — it is universal. Think of it as an automatic lab notebook: after every meaningful experiment, you record what happened, what worked, what didn't, and what to try next.
---

# Project Journal

A universal, automatic project journal that keeps every active project's state documented and ready for instant pickup — from any new session, at any time.

## Why this exists

Work happens across parallel sessions. Context gets lost between them. A session may run out of context window, get archived, or simply end. Without a written record, the next session starts blind — repeating mistakes, re-discovering file locations, losing hard-won learnings.

The project journal solves this by maintaining a detailed journal inside the project's own folder — so the full state, history, and learnings travel with the project and are available on demand.

## Architecture

```
<project-folder>/
└── .project-journal/
    ├── STATE.md                       # Current state: what exists, what's done, what's next
    ├── CHANGELOG.md                   # Append-only log of significant changes
    ├── LEARNINGS.md                   # What works, what doesn't, errors & fixes
    └── FILES.md                       # Key file map with descriptions
```

## When to run

Run this skill as the **last step** of any session that:

- Created or significantly edited project files
- Made decisions that affect project direction
- Tried approaches that succeeded or failed (especially failures — they're the most valuable to record)
- Produced deliverables
- Encountered errors worth documenting
- Changed project status (started, blocked, delivered, etc.)

Also run when explicitly asked to save state, update journal, or prepare for session handoff.

If the `.project-journal/` folder doesn't exist yet for a project, create it. This is how new projects get initialized.

## What to do

### Step 1: Identify the project

Determine which project was worked on. Use context cues:
- The mounted/selected folder name
- Files that were created or edited
- The topic of conversation

If this is a new project (no `.project-journal/` folder), initialize it fresh. If it exists, read the current state first.

### Step 2: Read current state (if exists)

Read these files to understand what's already tracked:
- `<project-folder>/.project-journal/STATE.md`
- `<project-folder>/.project-journal/CHANGELOG.md`
- `<project-folder>/.project-journal/LEARNINGS.md`

### Step 3: Update STATE.md

This is the main file any new session should read first. Keep it structured but readable. Include:

```markdown
# <Project Name> — Current State

**Last updated:** YYYY-MM-DD
**Status:** <In Progress | Blocked | Delivered | On Hold>
**Client/Context:** <who this is for, one line>

## What This Project Is
<2-3 sentence description>

## Current Status
<What's done, what's in progress, what's blocked. Be specific — file names, percentages, concrete state.>

## Key Files
<The 5-10 most important files with paths and one-line descriptions. Full file map is in FILES.md.>

## Open Issues
<Numbered list of things that need fixing or attention, with severity.>

## Next Steps
<Concrete, actionable items. Not vague "continue work" but specific "fix breakLine issue in batch1.js slides 3/4/13/31".>

## How to Resume This Project
<Step-by-step instructions for a fresh session to pick up where this one left off. Include: what to read first, what command to run, what the user's last instruction was.>
```

### Step 4: Update CHANGELOG.md

Append-only, newest first. One entry per session or significant event.

```markdown
## YYYY-MM-DD — <Brief title>

**What happened:**
- <Concrete actions taken, files created/modified>

**Decisions made:**
- <Any choices or direction changes>

**Errors encountered:**
- <What went wrong, with context>

**Result:**
- <What was delivered or achieved>
```

### Step 5: Update LEARNINGS.md

This is the most valuable file long-term. It captures institutional knowledge — the kind of thing that saves hours when starting a new session. Organize by category:

```markdown
# Learnings

## What Works
- <Approach/tool/pattern that succeeded, with context for WHY it works>

## What Doesn't Work
- <Approach that failed, with WHY it failed — not just "didn't work" but the root cause>

## Errors & Fixes
- **<Error description>** — Fix: <what solved it>. Root cause: <why it happened>.

## Strategies & Patterns
- <Reusable approaches discovered during this project>

## User Preferences
- <Things the user corrected or expressed preferences about — style, tools, workflow>
```

Important: write learnings as transferable knowledge, not just project-specific notes. "PptxGenJS mutates option objects" is useful anywhere. "Slide 3 needed more padding" is not.

### Step 6: Update FILES.md (if files changed)

A comprehensive file map. Only update if files were created, moved, or deleted.

```markdown
# Key Files

## Deliverables
- `path/to/file.ext` — Description, status (draft/final/abandoned)

## Build Scripts / Tools
- `path/to/script.js` — What it does, how to run it

## Source / Reference
- `path/to/source.pdf` — What it contains, who provided it

## QA / Testing
- `path/to/qa/` — What these files are for
```

### Step 7: Report to user

After updating, show a brief summary:

```
## Journal Updated

**Project:** <name>
**Files updated:** STATE.md, CHANGELOG.md, LEARNINGS.md
**Changelog entry:** <one-line summary of what was logged>
**New learnings:** <count> added
**Next session should:** <the single most important next action>
```

## Important rules

- **Never delete information** — only add, update, or mark as superseded
- **Changelog is append-only** — newest first
- **Learnings should be transferable** — write them so they're useful beyond this specific project
- **Dates in ISO format** (YYYY-MM-DD)
- **Paths should be relative** to the project folder where possible
- **Be concrete** — "fix breakLine in batch1.js line 273" not "fix formatting issues"
- **Capture the user's exact words** when they express preferences or reject something — these become feedback memories
- **State file should be self-sufficient** — a new session reading only STATE.md should be able to resume work without reading the full conversation history
