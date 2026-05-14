# backspaceoddity-skills

Public collection of [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) skills from [Backspace Oddity](https://backspaceoddity.com).

Skills that cover the basics of running projects with Claude Code: setting up a new project, keeping a journal of what happened, picking up context next time, proofing what you ship — plus a methodology skill for JTBD-driven competitive analysis.

## What's included

| Skill | What it does | When it runs |
|---|---|---|
| **`scaffold`** | Creates a new project folder with structure (`brief/`, `research/`, `deliverables/`, etc.) under a name you choose. Picks blocks via interactive checkboxes — client work, internal initiative, research, launch. | `/scaffold` or "create project structure" |
| **`project-journal`** | Maintains a living journal in `.project-journal/` — `STATE.md`, `CHANGELOG.md`, `LEARNINGS.md`, `FILES.md`. The lab notebook for your project. | End of session, or "save state" / "обнови журнал" |
| **`catch-up`** | Reads the journal + `CLAUDE.md` + saved context at the start of a session, so the next Claude works as if it were the same one from yesterday. | Start of every session, or "catch up" / "продолжай" |
| **`self-qa`** | Universal quality gate before any deliverable — checks correctness, architecture, security, spec compliance, visual integrity, content, and numeric provenance. Lenses, not a monolithic checklist. | Before presenting any deliverable |
| **`doc-proofreader`** | Six-pass review of any document before sharing — internal consistency, entity names, factual accuracy, structure, language, formatting. | Before publishing or "proofread" / "найди ошибки" |
| **`backspaceoddity-jtbd`** | Competitive analysis through the Jobs-to-be-Done lens — main job by the core-functional formula (Verb + Object + Contextual Clarifier), 4–6 substitutes across five categories (direct, indirect, status quo, manual workaround, do nothing), one falsifiable research question. | "JTBD", "конкурентный анализ", "substitutes", "competitive mapping" |

## How they fit together

The core five skills form a small system, not a random pile:

```
Start of session  →  catch-up         (read context)
During work       →  scaffold         (new project structure when needed)
                  →  self-qa          (before every delivery)
                  →  doc-proofreader  (before publishing documents)
End of session    →  project-journal  (write what happened)
```

`catch-up` and `project-journal` are paired — one writes at the end, the other reads at the start. Together they keep your projects pickup-able across sessions.

## Who this is for

Anyone using Claude Code who wants ready-made skills for everyday project work — without writing them from scratch.

These skills assume no specific stack, no specific tools, no specific knowledge graph. They work in any project folder, in any language, with default Claude Code.

## Installation

See [INSTALL.md](INSTALL.md) for full instructions. The short version:

```bash
# Clone the repo
git clone https://github.com/BackspaceOddity/backspaceoddity-skills.git ~/backspaceoddity-skills

# Install all skills globally (so they work in any project)
for d in ~/backspaceoddity-skills/skills/*/; do
  name=$(basename "$d")
  ln -s "$d" "$HOME/.claude/skills/$name"
done
```

Then restart Claude Code. Slash commands like `/scaffold` should appear.

## Philosophy

- **Universal, not opinionated.** Each skill works in any project without configuration.
- **Lenses, not monoliths.** `self-qa` and `doc-proofreader` use independent review passes — apply only what's relevant.
- **The user's words, not ours.** Skills capture the user's exact phrasing in journals and learnings, instead of paraphrasing.
- **Honest claims.** `self-qa` blocks "100% done" claims that aren't backed by evidence in the session transcript.

## Contributing

Found a bug, want to suggest a skill, or have an improvement? Open an issue or a pull request.

If you fork this collection for your own org's flavour — go ahead. These skills were extracted from a larger internal set by stripping team-specific machinery (knowledge graphs, living agents, branded fonts, client names). Adapt them back to your context.

## License

MIT (or specify your preferred license here).
