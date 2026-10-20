---
name: butterfly-effect
argument-hint: "[check] [OLD → NEW]"
description: Propagate a change EVERYWHERE in the project (code, texts, docs, config, tests, translations). Use whenever something is renamed or a name, text, value, price, rule, color, URL or behavior changes and may exist in several places. With no argument, automatically checks and fixes the most recent modifications. Asked only to check (check, --check, vérifie, what did I miss), it reports the misses without editing anything.
---

# Butterfly effect — one change, nothing forgotten

Goal: when something changes, EVERYTHING that depends on it changes too, across the whole project. Never stop at the first file found.

**Check mode (report only).** Use it when the argument or the user's request asks only to check, in any form or language (`check`, `--check`, `vérifie`, "dry run", "did I miss anything?"), alone (detect the changes as in step 0) or followed by a change (`check Basket → Cart`). No argument, or a change named alone (`Basket → Cart`), means normal mode. Run every step, but wherever a step says edit, fix, correct, rename, update or commit, write a report line instead, and skip the step 3 confirmation. Change NOTHING, inside or outside the repo: no file, memory, rename or commit, and no build or test run (they write files and can have side effects); a typecheck or lint only in a form that writes nothing (`tsc --noEmit`, lint without `--fix`). Report as in step 6, each miss as file:line, what is there, what it should be.

## 0. No argument: detect the latest modifications
If the user runs the skill without specifying the change, do NOT ask: find it yourself, in this order:
1. **Current conversation**: edits made in this session (files touched, old → new).
2. **Git** (if a repo): `git status` + `git diff` (uncommitted), then `git diff HEAD~1` / `git log -5 --stat` if nothing is uncommitted.
3. **No git**: project files sorted by modification time (most recent, e.g. last few hours), comparing with surrounding content to infer what changed.

For each modification found, infer OLD → NEW (name, text, value, rule, behavior), then treat each as a change to propagate (steps 2 to 6). Do a general check: don't stop at a single change, cover all recent ones. If a modification is truly ambiguous (intent can't be inferred), list it and ask only about that one.

## 1. Define the change
Write one line: OLD → NEW (name, text, value, behavior, rule). If ambiguous, ask before touching anything.

## 2. Map BEFORE editing
Search the ENTIRE project (excluding node_modules, .git, dist, build, lockfiles):
- **Form variants**: camelCase, PascalCase, snake_case, kebab-case, UPPERCASE, plural/singular, with/without accents, with/without spaces or hyphens.
- **Texts**: UI labels, titles, buttons, error messages, placeholders, emails, notifications, meta/SEO, alt, aria-label, README, docs, changelog, comments, CLAUDE.md.
- **Translations**: all language files (i18n/locales/.json/.po/.arb/.strings), every language, not just one.
- **Non-code files**: config, .env.example, JSON/YAML/TOML, SQL/migrations, seeds, fixtures, mocks, snapshots, CI/CD, Docker, scripts, manifests.
- **Indirect references**: imports, routes/URLs, config keys, file/folder names, dynamically built strings, duplicated constants, types/interfaces/schemas.
- **Tests**: unit, e2e, snapshots, test data.
- **Derived values**: if a value changes (price, limit, version, date), also look for its computed equivalents or spelled-out forms.
- **Concepts without the exact word**: also search by meaning (synonyms, description of the behavior), not just the literal term.
- **Claude's memory**: list `~/.claude/projects/`, open the folder matching this project's path (every `/`, `\`, `:` and space becomes `-`: `/home/me/app` → `-home-me-app`, `C:\my app` → `C--my-app`) and read its `MEMORY.md` and `memory/` files for facts the change made false (lists, names, counts, rules). In step 4 correct them in place, leave dated or history notes unchanged, and report each edit old → new (outside git: the report is the only undo). Also read the global `~/.claude/CLAUDE.md`, but never edit it: flag what it gets wrong.

### Follow the flow, not just the word
Grep finds the term; it does not find wrong behavior. For each change, read the code and answer:
- **Producers**: every place that creates, computes or sets this value/state/text.
- **Consumers**: every place that reads, displays, sends, filters, sorts or branches on it (UI, notifications, exports, reports, scheduled jobs, rules).
- **Duplicates**: is the same thing now done by two paths (sent twice, shown twice, counted twice)? Does an old path still fire alongside the new one?
- **Old-shape assumptions**: if the meaning or shape changed (an action became an observation, a fixed list became a variable one), does every consumer still treat it right (section, priority, wording, count)? Check hardcoded counts, lengths, orderings and enum lists, and `else`/`default` branches or negative conditions (`!= DONE`, `not in`) that never name the value but now silently misroute it.

## 3. Show the plan
List file by file what will be modified (and what is deliberately left alone, with the reason: history, past changelog, quotes, user data). If > 20 files or a destructive change, ask for confirmation.

## 4. Apply everywhere
Edit every listed place, skipping none. Keep the style and tone of the surrounding text (formal/informal address, capitalization, punctuation). Rename files/folders too if needed.

- **Tests expecting the old value**: update the expectation only when the change is intended, and list every modified expectation in the report (test name, old expected → new expected) so the user can validate it. Never weaken or delete a test to make it pass; if a test fails for another reason, report it instead.
- **Regression tests**: for each missed propagation fixed that no existing test covers, propose a test (name + what it asserts) in the report. Do not write it unless the user asks.
- **Project conventions**: if the project has them, follow them after propagating (version bump, changelog entry, generated files to rebuild, docs index). Look at recent history to see how it is usually done; don't invent a convention the project doesn't have.

## 5. Verify (mandatory)
- Re-grep the OLD term in all its variants: only justified exceptions may remain.
- Run build / typecheck / lint / tests if available. First check what each script runs (package.json, Makefile): if it deploys, restarts a service, touches a real database, sends real messages or calls a production service, don't run it; list it as "to run by hand". Fix only breakage the propagation caused; report the rest.
- Re-read places where text changed for consistency (grammar, agreement, gender, plurals).

## 6. Report
Open with what was checked: one line per change (OLD → NEW, source: named by the user / conversation / git / file dates), plus any scope not searched or command not run, so a gap is visible.

Split the findings into two separately numbered groups, and never mix them:
- **Missed propagation**: the change is clear and one place did not follow it. Only one correct fix exists. In normal mode these are fixed; in check mode they are listed.
- **Decisions needed**: a consequence of the change where several answers are valid, or that alters data, money, counts or business rules (e.g. include or exclude an item from a total, adjust a baseline). Never fix these on your own, in any mode: state the options and their effect, then ask.

Head each group with its count (`Missed propagation: 3`), then recount the numbered items and fix the header if they differ. Every other count in the report must match too. Proposed tests are never counted in either group.

Then:
- Number of files modified, grouped (code / texts / docs / config / tests); exceptions left and why; build/test result.
- To validate: each test expectation changed (test, old → new), each memory fact corrected or to correct (file, old → new), each proposed regression test (name + what it asserts).
- Outside-the-repo checklist, answered point by point (never a blanket "nothing external"). For each: "not applicable", "checked: OK", or "to check by hand" with the reason:
  - Database / stored data still holding the old term or value
  - Local data files, saved state, history already written with the old form
  - Caches (app, build, CDN, browser)
  - Deployed service / running process still on the old version
  - Messages or notifications already sent, or queued, with the old wording
  - Scheduled jobs, cron, webhooks, external integrations, API consumers
  - Copies held by outside services, e.g. payment prices (a Stripe price's amount can't be edited, so a change needs a new price ID, and existing subscriptions stay on the old one), hosted email templates, old URLs in sent emails or search results (is there a redirect?)
  - Environment variables and secrets on other machines

## Rules
- Never declare "done everywhere" without step 5.
- Don't modify history (past changelog, already-applied migrations, git) unless explicitly asked.
- When in doubt about a place, flag it rather than silently modifying it.

## Git (if the project is a repo)
- **Before**: `git status`. If unrelated uncommitted changes exist, flag them so they aren't mixed with the propagation (suggest a commit or `git stash`, never do it without consent).
- **During**: `git diff` / `git log -5 --stat` to find the latest modifications (step 0); `git grep` for mapping (respects .gitignore, fast).
- **After**: `git diff --stat` to summarize what was propagated and check that no unexpected file changed.
- **Commit**: suggest a clear message (`Propagate: OLD → NEW`) but only commit if the user asks. Never push, reset --hard, rebase or rewrite history without an explicit request.
- **Rollback**: if something goes wrong, explain how to undo (`git restore <file>` or `git diff` to review), without running it on your own.
- **No repo**: suggest `git init` so the next check is more precise.
