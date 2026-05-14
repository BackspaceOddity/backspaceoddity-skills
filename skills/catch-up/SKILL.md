---
name: catch-up
description: Session startup skill that reads all project context before doing any work. MUST be used at the START of every new Claude Code session when a project folder is mounted — before answering questions, before writing code, before any task. Also trigger when the user says 'catch up', 'catch-up', 'что нового', 'введи в курс', 'read the context', 'get up to speed', 'resume', 'продолжай', 'где мы остановились', 'what did we do last time', or any phrase that implies the user wants Claude to understand current project state. Do NOT skip this skill even if the task seems simple — stale context causes wrong decisions.
---

# Catch-Up: Session Context Loader

Read all available project context so you can work as if you were the same Claude that worked on this project last time. This is the first thing you do in any session — before responding to the user's task.

Pairs with the `project-journal` skill: that one writes the journal at the end of a session, this one reads it at the start.

## Step 0: Sync with team (if git repo)

If the current project is a git repo, pull the latest changes before reading anything:

```bash
cd "<mounted-folder>"
git pull --rebase origin main 2>/dev/null || true
```

This ensures you're reading the latest state — not a stale version from yesterday. Important for any shared repo where multiple team members push to main.

If `git pull` fails (dirty working tree, no remote, no upstream), proceed silently — better to work with slightly stale data than to block the session.

## Step 1: Read the Project Journal

The project journal lives in `<mounted-folder>/.project-journal/`. Read these files in this order:

1. **STATE.md** — current project state, what's done, what's next, how to resume. This is the most important file.
2. **LEARNINGS.md** — what works, what doesn't, errors and fixes. This prevents you from repeating past mistakes.
3. **CHANGELOG.md** — recent history. Read the latest 2–3 entries to understand what happened in recent sessions.
4. **FILES.md** — key file map with descriptions. Skim to know where things are.

If `.project-journal/` doesn't exist, skip to Step 2.

## Step 2: Check for CLAUDE.md

Look for `CLAUDE.md` or `.claude/CLAUDE.md` in the mounted folder. If it exists, read it — it may contain project-specific instructions, conventions, or rules.

## Step 3: Read Saved Context

Check for a `context/` folder (lowercase) in the project root. This folder holds source materials — briefs, exports, reference documents — saved during sessions.

```bash
ls <mounted-folder>/context/ 2>/dev/null
```

If files exist, read them. These are the original inputs the user provided — proposals, briefs, links. Reading them gives full context even when the session that received them is gone.

Key files to read if present: anything named `*brief*`, `*proposal*`, `*context*`, `*framing*`.

## Step 4: Report to the User

After reading everything, give the user a brief status report. Keep it short — 5–10 lines max. Include:

- **Project name and status** (from STATE.md)
- **Last session summary** (from latest CHANGELOG entry — 1–2 sentences)
- **Saved context** (from `context/` — list what source materials are available)
- **Key open issues** (from STATE.md — numbered, max 3)
- **Recommended next step** (from STATE.md or your judgment)

Format example:

```
**Project:** <Project Name>
**Status:** In Progress — three deliverables done, one pending review
**Last session:** May 12 — drafted IC charter, sent for review to client
**Open issues:** (1) Section 4 needs rework after feedback, (2) Pricing model in progress
**Next step:** Wait for feedback or pick up Section 4 rework

Ready to work. What's next?
```

Adapt the language to the user's language (Russian if they speak Russian, English otherwise).

## Important Rules

- **Never skip this skill.** Even if the user's request seems unrelated to prior work, context prevents mistakes.
- **Don't dump raw file contents to the user.** Synthesize. The user doesn't want to see STATE.md — they want to know you're up to speed.
- **If files don't exist, say so briefly** and offer to set up the project journal.
- **This skill takes ~30 seconds.** That's a worthwhile investment vs. hours lost to stale context.
- **After catch-up, proceed to the user's task immediately.** Don't ask "shall I continue?" — just start working.
