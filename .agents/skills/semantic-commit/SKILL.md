---
name: semantic-commit
description: Groups all current changes into semantic commits (feat, fix, refactor, test, docs, chore, ci, config) and pushes the branch. Use this whenever the user asks to commit, push, save, or "checkpoint" their work — even casually ("commit this", "sube esto", "push it", "guarda los cambios") — or when a task is finished and the working tree has uncommitted changes that should be committed. Do not use it for merge, rebase, tag, or release operations.
---

# semantic-commit — Group changes into semantic commits and push

Group all current changes into semantic commits and push the current branch.

Optional message context: `${ARGUMENTS}`

## Inspect

Run before planning: `git status --short`, `git diff --stat`, `git diff`, `git stash list`, `git log --oneline -10`, `git branch --show-current`. For untracked files (not covered by `git diff`), read their contents before grouping them.

If the working tree is clean, say so and stop — there is nothing to commit.

## Safety

- Sensitive files (`.env`, `*.key`, `*.pem`, tokens, secrets, credentials) → **stop and ask**. Never stage them silently.
- Untracked files that look like artifacts (`dist/`, `build/`, `node_modules/`, logs, coverage) → don't stage them; suggest adding to `.gitignore` instead.
- On `main`/`master` → confirm before pushing.
- Never use `--no-verify`, `--force`, or `--amend`. Never revert or discard changes.
- Never touch the stash — `git stash list` is for awareness only.

## Grouping

- Group files by intent: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `ci`, `config`. Never mix intents in one commit.
- Add a scope when changes are clearly scoped to a module/dir/feature (`type(scope): message`); infer it from paths or `$ARGUMENTS`. Omit only when cross-cutting.
- Collapse multiple same-type/scope files that would each be trivial into one commit:
  - `git commit -m "type(scope): summary" -m "<body>"`
  - Body: one bullet per file (what + why). Only add a body for 2+ collapsed files.
- Match the style from `git log --oneline -10`. Use `$ARGUMENTS` only if it reflects the actual changes.
- **Spec-branch convention:** if the current branch matches `spec-NN-slug` and the recent log uses `spec-NN: …` messages, follow that convention instead of the generic types.

## Plan

Show each group, its message, and files. Proceed if unambiguous; ask only on real uncertainty.

## Execute

Per group: `git add <files>` then `git commit`.

After all commits, **ask before pushing**:

```
N commits created on <branch>. Push to remote? [y/N]
```

- **Yes** → `git push`.
- **No / empty** → do not push. Say the commits stay local and can be pushed later with `git push`.

If the push fails (no upstream, rejected, no network), report the exact error and the suggested fix (`git push -u origin <branch>` for missing upstream). Do not retry with `--force`.

## Summary

List each commit (hash + message) and the branch, stating whether it was pushed or the commits remain local.
