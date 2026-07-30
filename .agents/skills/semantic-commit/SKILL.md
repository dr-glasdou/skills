---
name: semantic-commit
description: Groups all current changes into semantic commits (feat, fix, refactor, test, docs, chore, ci, config) and pushes the branch. Use this whenever the user asks to commit, push, save, or "checkpoint" their work — even casually ("commit this", "sube esto", "push it", "guarda los cambios") — or when a task is finished and the working tree has uncommitted changes that should be committed. Do not use it for merge, rebase, tag, or release operations.
---

# semantic-commit — Group changes into semantic commits and push

Group all current changes into semantic commits and push the current branch.

Optional message context: `$ARGUMENTS`

## Inspect

Run before planning: `git status --short`, `git diff --stat`, `git diff`, `git diff --cached`, `git stash list`, `git log --oneline -10`, `git branch --show-current`.

- `git diff` shows unstaged work only — `git diff --cached` catches anything already staged, so nothing is dropped or mis-grouped.
- For untracked files (not covered by `git diff`), read their contents before grouping.
- For deleted or renamed files (status `D` / `R`), don't try to read them — infer intent from the diff status and the path.

If the working tree is clean (nothing staged or unstaged), say so and stop — there is nothing to commit.

## Safety

- Sensitive files (`.env`, `*.key`, `*.pem`, tokens, secrets, credentials) → **stop and ask**. Never stage them silently.
- Untracked files that look like artifacts (`dist/`, `build/`, `node_modules/`, logs, coverage) → don't stage them; suggest adding to `.gitignore` instead.
- On `main`/`master`, pushing needs a **second explicit confirmation** beyond the normal push prompt — name the branch and ask again.
- Never use `--no-verify`, `--force`, or `--amend`. Never revert or discard changes.
- Never touch the stash — `git stash list` is for awareness only.

## Grouping

- Group by intent: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `ci`, `config`. Never mix intents in one commit.
- Add a scope when changes are clearly scoped to a module/dir/feature (`type(scope): message`); infer it from paths or `$ARGUMENTS`. Omit only when cross-cutting.
- **A single file may hold more than one intent** (e.g. a `feat` and an unrelated `fix`). When that happens, stage by hunk with `git add -p <file>` so each intent lands in its own commit. Only fall back to whole-file staging when the file is genuinely one intent.
- Collapse multiple same-type/scope files that would each be trivial into one commit:
  - `git commit -m "type(scope): summary" -m "<body>"`
  - Body: one bullet per file (what + why). Only add a body for 2+ collapsed files.
- Match the style from `git log --oneline -10`. Use `$ARGUMENTS` only if it reflects the actual changes.
- **Spec-branch convention:** if the current branch matches `spec-NN-slug` and the recent log uses `spec-NN: …` messages, follow that convention instead of the generic types.

### Message rules (Conventional Commits)

- Subject in **imperative mood**, **lowercase** after the type, **no trailing period**, aim for ≤50 chars.
- **Breaking changes:** mark with `!` after the type/scope (`feat!:`, `refactor(api)!:`) **and** add a `BREAKING CHANGE: <what broke + migration>` footer. Never bury a breaking change in a normal subject.
- Body (when present) explains *what and why*, not how — wrap it in a second `-m`.

## Ordering

Commit in dependency order so history reads correctly: a `refactor` that a later `feat` builds on is committed first. When there's no dependency, default to: `config`/`chore` → `refactor` → `feat` → `fix` → `test` → `docs`.

## Plan

Show each group in commit order, its message, and files. Proceed if unambiguous; ask only on real uncertainty.

## Execute

Per group: `git add <files>` (or `git add -p <file>` for hunk-level) then `git commit`.

**If a pre-commit hook fails:** report the exact failure, stop, and let the user fix it. Do not retry with `--no-verify` or work around the hook.

After all commits, **ask before pushing**:

```
N commits created on <branch>. Push to remote? [y/N]
```

- **Yes** → `git push` (on `main`/`master`, confirm a second time first — see Safety).
- **No / empty** → do not push. Say the commits stay local and can be pushed later with `git push`.

If the push fails (no upstream, rejected, no network), report the exact error and the suggested fix (`git push -u origin <branch>` for missing upstream). Do not retry with `--force`.

## Summary

List each commit (hash + message) and the branch, stating whether it was pushed or the commits remain local.
