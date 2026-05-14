# Installing bso-skills

Two questions to decide first:

1. **Scope** — do you want the skill in every project (global) or just one (per-project)?
2. **Mode** — symlink (auto-updates with `git pull`) or copy (stable snapshot)?

Defaults below assume **global + symlink** — works for most cases. Other options at the bottom.

## Prerequisites

- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) installed
- `git` available in your shell

## Default install — all skills, global, via symlinks

### 1. Clone the repo

```bash
git clone https://github.com/BackspaceOddity/bso-skills.git ~/bso-skills
```

You can clone anywhere — `~/bso-skills` is just the convention used in the snippets below. If you clone elsewhere, adjust the paths.

### 2. Symlink each skill into Claude Code's global skills folder

```bash
mkdir -p ~/.claude/skills

for d in ~/bso-skills/skills/*/; do
  name=$(basename "$d")
  ln -s "$d" "$HOME/.claude/skills/$name"
done
```

### 3. Restart Claude Code

Quit and reopen. Claude Code reads `~/.claude/skills/` at startup.

### 4. Verify

Open Claude Code in any project. Type `/` — you should see at least `/scaffold` among the available commands. Run `/scaffold` in an empty folder to test.

If a skill doesn't appear:
- Check the symlink: `ls -la ~/.claude/skills/scaffold/` should show `SKILL.md`
- Check Claude Code's skill discovery log (varies by version)
- Make sure you restarted Claude Code after symlinking

## Alternative: install one skill only

If you only want, say, `project-journal`:

```bash
ln -s ~/bso-skills/skills/project-journal ~/.claude/skills/project-journal
```

Restart Claude Code. Done.

## Alternative: per-project install (not global)

If you want a skill active only in one specific project:

```bash
cd /path/to/your-project
mkdir -p .claude/skills
ln -s ~/bso-skills/skills/scaffold .claude/skills/scaffold
```

The skill will be loaded only when Claude Code opens that project folder.

## Alternative: copy instead of symlink

If you want a stable snapshot that doesn't change when you `git pull`:

```bash
for d in ~/bso-skills/skills/*/; do
  name=$(basename "$d")
  cp -r "$d" "$HOME/.claude/skills/$name"
done
```

Trade-off: you won't get updates without re-running this. Use symlinks for living tracking, copy for stability.

## Updating

If you installed via symlinks:

```bash
cd ~/bso-skills
git pull
```

That's it — symlinks pick up the new content automatically.

If you installed via copy: re-run the install command. Or run a sync:

```bash
cd ~/bso-skills && git pull
for d in ~/bso-skills/skills/*/; do
  name=$(basename "$d")
  rm -rf "$HOME/.claude/skills/$name"
  cp -r "$d" "$HOME/.claude/skills/$name"
done
```

## Uninstalling

Symlinks:

```bash
for d in ~/bso-skills/skills/*/; do
  name=$(basename "$d")
  rm "$HOME/.claude/skills/$name"
done
```

Copies: same command (`rm -rf` instead of `rm`):

```bash
for d in ~/bso-skills/skills/*/; do
  name=$(basename "$d")
  rm -rf "$HOME/.claude/skills/$name"
done
```

You can keep `~/bso-skills/` cloned for reference, or delete it:

```bash
rm -rf ~/bso-skills
```

## Troubleshooting

**Slash command doesn't appear after install.** Make sure Claude Code was fully restarted (not just refreshed). Some versions discover skills only at startup.

**Symlink shows up but skill doesn't load.** Check the symlink target exists and is a directory with `SKILL.md` inside:

```bash
ls -la ~/.claude/skills/scaffold/SKILL.md
```

**Permission denied on symlink.** On some systems `~/.claude/skills/` may not be writable. Check `ls -la ~/.claude/` and adjust permissions if needed.

**Skill behaves differently than docs say.** Each skill is in `~/bso-skills/skills/<name>/SKILL.md` — read the `description` and body for that skill's exact triggers and behaviour. The SKILL.md is the source of truth.

## Where each skill lives once installed

After running the default install, you'll have:

```
~/.claude/skills/
├── scaffold/         → symlink → ~/bso-skills/skills/scaffold/
├── project-journal/  → symlink → ~/bso-skills/skills/project-journal/
├── catch-up/         → symlink → ~/bso-skills/skills/catch-up/
├── self-qa/          → symlink → ~/bso-skills/skills/self-qa/
└── doc-proofreader/  → symlink → ~/bso-skills/skills/doc-proofreader/
```

Each contains a `SKILL.md` file — that's the entire skill.
