# butterfly-effect

A Claude Code skill that propagates a change **everywhere** in a project: code, texts, translations, docs, config, tests.

## What it does
- Maps every occurrence (case variants, plurals, UI texts, i18n, docs, config, tests).
- Shows the plan before editing, applies everywhere, then re-checks that no old term remains.
- Runs build / lint / tests and returns a short report.
- **With no argument**, it finds the latest modifications on its own (conversation, `git diff`, or recent files) and checks their propagation.

## Install
Copy `skills/butterfly-effect` into `~/.claude/skills/` (global) or `.claude/skills/` (project).

## Usage
```
/butterfly-effect "Basket" → "Cart"
/butterfly-effect
```

For near-automatic triggering, add this to `~/.claude/CLAUDE.md`:
> After any rename or change of a text, value or rule, invoke the `butterfly-effect` skill.

## License
MIT
