# butterfly-effect

A Claude Code skill that propagates a change **everywhere** in a project: code, texts, translations, docs, config, tests.

## What it does
- Maps every occurrence (case variants, plurals, UI texts, i18n, docs, config, tests).
- Shows the plan before editing, applies everywhere, then re-checks that no old term remains.
- Runs build / lint / tests and returns a short report.
- Follows the flow, not just the word: producers, consumers, duplicate paths.
- Answers an outside-the-repo checklist (database, caches, deployed service, sent messages, scheduled jobs).
- Fixes facts made false in Claude's memory files and CLAUDE.md.
- Splits the report into *missed propagation* (fixed) and *decisions needed* (asked, never fixed alone).
- Proposes a regression test for each fix that no test covers.
- **`check` mode**: reports what was missed without editing anything.
- **With no argument**, it finds the latest modifications on its own (conversation, `git diff`, or recent files) and checks their propagation.

## Install
Copy `skills/butterfly-effect` into `~/.claude/skills/` (global) or `.claude/skills/` (project).

To stay up to date with this repo, clone it and link the folder instead of copying:

```
# macOS / Linux
ln -s "$(pwd)/skills/butterfly-effect" ~/.claude/skills/butterfly-effect

# Windows (PowerShell)
New-Item -ItemType Junction -Path "$HOME\.claude\skills\butterfly-effect" -Target "$PWD\skills\butterfly-effect"
```

The skill is pinned to the `opus` model (`model:` line in the frontmatter); remove that line to use your session's model.

## Usage
```
/butterfly-effect "Basket" → "Cart"
/butterfly-effect
/butterfly-effect check
```

For near-automatic triggering, add this to `~/.claude/CLAUDE.md`:
> After any rename or change of a text, value or rule, invoke the `butterfly-effect` skill.

## License
MIT
