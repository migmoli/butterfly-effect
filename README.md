# butterfly-effect 🦋

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE) ![Claude Code skill](https://img.shields.io/badge/Claude%20Code-skill-d97757)

A Claude Code skill that propagates a change **everywhere** in a project — code, texts, translations, docs, config, tests — and tells you what it could not see.

You rename something, change a label, a value or a rule. Claude edits the file in front of it and says "done". Then you find the old word in an email template, a test that still expects the old value, and a notification that now fires twice. This skill is the pass that catches those.

## Example

`/butterfly-effect check` after a session that renamed two status labels and turned a fixed list of six tracked items into a variable one. Adapted from a real run; names changed.

```
Check only, nothing was modified.

Checked (from this conversation):
  WATCH → DEGRADING · new label RISING · tracked items: fixed 6 → variable

Missed propagation: 2

1. server.py:2952 — the "outside selection" text uses the declared maximum,
   not the number of names.
   Now:       "outside your 6 tracked items (A, B, C)"
   Should be: "outside your 3 tracked items (A, B, C)"

2. Duplicate notification on DEGRADING. When a line switches to DEGRADING,
   two messages are sent: the health card sends the label change, and the
   rules scan also sends the health rule (server.py:7077).
   The duplicate was removed for WATCH, not for DEGRADING.

Decisions needed: 1

1. The comparison chart (server.py:1546). The "your items" basket now includes
   item A, but the market it is compared against excludes it. Either exclude A
   from the basket, or include it in the market. Which one?

To validate
  Memory fact to correct: project note lists the old six items → A, B, C.
  Proposed test: test_digest_rising_is_not_an_action
                 (RISING never appears in the "to do" line).

Outside the repo
  Stored data            Checked, OK: no stored value uses the old labels.
  Local history files    Checked, OK: past records stay as written.
  Caches                 Checked, OK: recomputed on the next pass.
  Running service        Checked, OK: on the current version.
  Messages already sent  Not applicable: nothing queued.
  Scheduled jobs         To check by hand: the scheduled task is still disabled.
  Outside services       Not applicable.
  Environment variables  Not applicable.
```

Neither of the two misses contains the old word: grep alone would not have found them.

## What it does

- **Maps before editing.** Every occurrence across the project: case variants, plurals, UI texts, every language file, docs, config, fixtures, tests, file names.
- **Follows the flow, not just the word.** Who produces the value, who consumes it, whether two paths now do the same thing, whether hardcoded counts still hold.
- **Shows the plan, applies everywhere, then re-checks** that no old term remains, and runs build / lint / tests — but never a script that deploys, touches a real database or sends real messages.
- **Separates fixes from decisions.** A missed propagation has one correct fix and gets fixed. Anything that touches data, money, counts or business rules is asked, never fixed on its own.
- **Answers an outside-the-repo checklist** point by point — database, stored data, caches, running service, sent messages, scheduled jobs, outside services (payment prices, hosted email templates, old URLs), environment — instead of "nothing external".
- **Keeps Claude's memory honest.** Corrects facts the change made false in the project's memory files, reports each edit old → new, and only flags the global `~/.claude/CLAUDE.md`.
- **Handles tests carefully.** Updates expectations that targeted the old value and lists each one for you to validate; proposes a regression test for fixes no test covers. Never weakens a test to make it pass.
- **Respects git.** Never commits, pushes or rewrites history unless you ask.

## Usage

```
/butterfly-effect "Basket" → "Cart"    propagate a named change
/butterfly-effect                      find the latest modifications and propagate them
/butterfly-effect check                report what was missed, edit nothing
```

Check mode also answers to `--check`, `vérifie` or "what did I miss?", alone or followed by a change (`check Basket → Cart`).

With no argument, it finds the recent changes itself: the edits of the current conversation, then `git diff` and recent commits, then recently modified files if the project is not a git repo.

To have Claude run it on its own, add this to `~/.claude/CLAUDE.md`:

> After any rename or change of a name, text, value or rule that may exist in several places, invoke the `butterfly-effect` skill. Do not invoke it for small local edits with no impact elsewhere.

## Install

In Claude Code (2.1.275 or later):

```
/plugin install butterfly-effect --marketplace migmoli/butterfly-effect
```

Then start a new session. Updates come through `/plugin`.

**Manual install.** Copy `skills/butterfly-effect` into `~/.claude/skills/` (all your projects) or `.claude/skills/` (one project). To stay up to date with this repo, clone it and link the folder instead of copying:

```
# macOS / Linux
ln -s "$(pwd)/skills/butterfly-effect" ~/.claude/skills/butterfly-effect

# Windows (PowerShell)
New-Item -ItemType Junction -Path "$HOME\.claude\skills\butterfly-effect" -Target "$PWD\skills\butterfly-effect"
```

The skill runs on your session's model. The flow check needs careful reading, so a strong model gives the best results.

## Limits

- It reads code and files. It cannot see a remote database, another machine or a third-party service; those end up as "to check by hand" in the checklist.
- On a very large repository the mapping step is slow. Name the change, or point it at a folder.
- It is a checklist Claude follows, not a static analyzer. Use `check` first when the stakes are high, and review the diff.

## License

MIT
