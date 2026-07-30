---
name: spec-impl
description: Implements an approved spec. Validates that the state means "Approved" (in any language), creates a git branch named after the spec, switches to it, and starts the implementation step by step with pauses to review diffs.
disable-model-invocation: true
argument-hint: <NN-spec-name>
allowed-tools: Bash(git status:*), Bash(git branch:*), Bash(git checkout:*), Bash(git log:*), Bash(cat:*), Bash(ls:*)
---

# /spec-impl — Implementer of approved specs

## Session context

Repository state:
!`git status --short`

Current branch:
!`git branch --show-current`

Spec branches (local + remote):
!`git branch -a --list "*spec-*"`

Specs available:
!`ls specs/ 2>/dev/null || echo "The specs/ folder does not exist"`

Branch-creation config:
!`cat specs/.spec-config.yml 2>/dev/null || echo "AutoCreateBranch: true (default, no config file)"`

---

Follow the phases in strict order. Do not advance if the previous phase didn't complete correctly.

## Phase 1 — Identify the spec

Argument: `$ARGUMENTS`

If empty: list `specs/`, ask for the exact name, stop and wait.

If set: find the file in `specs/` — the user may give the full name (`01-mvp-arkanoid`), just the number (`01`), or just the slug (`mvp-arkanoid`). If not found, show available specs and ask for a correction. If found, continue.

## Phase 2 — Validate state

Read the spec file (`cat` or Read tool).

**Parse the state positionally:** the header blockquote's **first line** is always the `Status`/`Estado`/equivalent line (enforced by `template.md`). Take the value after the colon on that line — don't search the rest of the file, don't infer from wording elsewhere.

**You may only continue if the state means "Approved"**, in any language: `Approved`, `Aprobado`, `Aprovado`, `Approuvé`, `Genehmigt`, `Approvato`, or any other word clearly meaning approved. Anything else — Draft, In review, Implemented, Obsolete, unrecognized, or state line not found — **stops** with the error below.

If unsure whether a value means approved, do not assume — stop and ask the user to clarify or update the spec.

**Error message (state not Approved):**

```
❌ I cannot implement this spec.

Current state: [STATE FOUND]
I only work with specs whose state means "Approved" (e.g. `Approved`, `Aprobado`,
or the equivalent in another language).

To continue you have two options:
  1. If ready, open the spec and change the state to "Approved" (or your
     team's equivalent) manually. That change is made by the human, not the agent.
  2. If it still needs work, use /spec [name] to resume it.
```

Don't offer to "start anyway". The block is intentional.

## Phase 2.5 — Staleness check

Before touching git: derive the branch name `spec-NN-slug` (as in Phase 3). If that branch already exists (see spec-branches list above), compare `git log -1 --format=%cI -- specs/NN-slug.md` against `git log -1 --format=%cI spec-NN-slug`. If the spec file is newer than the branch's last commit, warn:

```
⚠️ This spec was edited after the last commit on spec-NN-slug.
The branch may be implementing an older version.

Continue anyway? [y/N] — or re-read the current spec first?
```

Wait for explicit confirmation. If no branch exists yet, skip silently.

## Phase 3 — Branch and switch

1. **Clean-tree check first.** From `git status --short`: if there are uncommitted changes, stop before any checkout:

   ```
   ❌ Your working tree has uncommitted changes:

   [paste short status]

   Commit or stash them before I switch branches — a dirty switch would
   carry these onto spec-NN-slug silently.
   ```

   Wait for the user to commit/stash and confirm, or to explicitly opt to carry them over. Don't decide for them.

2. Branch name = spec file name without extension: `spec-NN-slug` (`01-mvp-arkanoid.md` → `spec-01-mvp-arkanoid`).

3. Read `AutoCreateBranch` from the config above. Missing / unset / unrecognized → `true` (default). Only an explicit `false` (any capitalization) disables auto-creation.

   **`true`:** proceed without asking. If the branch doesn't exist, `git checkout -b spec-NN-slug`. If it exists, tell the user (may mean resuming — see Phase 3.5). Either way, `git checkout spec-NN-slug` and confirm the switch succeeded.

   **`false`:** ask first —

   ```
   AutoCreateBranch is set to false.
   Create and switch to the branch spec-NN-slug? [y/N]
   ```

   Yes → create/switch as above. No/empty → don't create any branch; say you'll implement on the current branch and ask for explicit confirmation to continue there. Wait for the answer.

4. Confirm:

   ```
   ✅ Ready to implement.

   Spec:   specs/NN-slug.md
   Branch: spec-NN-slug  (active)   (← or current branch, if none created)
   State:  Approved   (← echo the actual value found)
   ```

5. **Don't implement yet.** Show the spec summary, extracting via the section anchors (`<!-- section:header -->`, `scope`, `plan`, `acceptance`) rather than heading text — works in any language. Show objective, scope, implementation plan, acceptance criteria. If the spec predates anchors, fall back to matching headings by meaning and flag once that it's missing anchors.

## Phase 3.5 — Resume detection (only if the branch already existed)

If Phase 3 found `spec-NN-slug` already existed: read the implementation-plan checkboxes in the spec file — the highest `- [x]` step is the last completed one (boxes are the source of truth, committed alongside each step's code). If any are ticked:

```
This branch already has Step 1..N marked done.
Resume from Step N+1?  [y/N]  — or review/redo an earlier step first?
```

Wait for confirmation; follow their lead if they want to redo (untick and re-run that step). If no boxes are ticked, proceed to Phase 4 at Step 1, noting briefly that none were done.

## Phase 4 — Implement step by step

After the summary (and Phase 3.5 if applicable):

```
I'll implement the spec following the plan exactly, pausing after each step
so you can review the diff.

Shall we start with Step [1 or N+1]?
```

Wait for explicit confirmation ("yes", "go", or equivalent). Then:

**Above all:** implement what the spec says. If something looks suboptimal, note it as an observation but implement what was agreed. Spec changes go in the spec, not silently into code.

**Rhythm per step:**
1. Implement the step's code.
2. Summarize files touched and what changed. Say: `Step N implemented. Review the diff — approve and I'll mark it done and commit.`
3. **Wait for explicit approval.** Do not tick or commit before it.
4. On approval: tick the step's checkbox in `specs/NN-slug.md` (`- [ ]` → `- [x]`).
5. Commit the code **and** the ticked spec together, with `Step N` in the message (e.g. `feat(spec-nn): Step 3 - wire scoring to collision events`).
6. Say `Step N done and committed. Continue with Step N+1?` and wait.

**On unresolved ambiguity:** stop, describe it exactly, offer 2–3 concrete options, wait. Don't improvise.

**On out-of-scope requests:** remind them it's out of scope, suggest noting it for the next spec, don't implement it here.

**After the last step:**

```
✅ All plan steps implemented.

Next: verify acceptance criteria one by one. If all pass, update the spec's
state to "Implemented" (or your repo's equivalent) and make the final commit
before merging this branch.
```

---

## Expected behavior

```
/spec-impl 01-mvp-arkanoid
  P1    Finds specs/01-mvp-arkanoid.md
  P2    Status (line 1 of blockquote) = "Approved" → ✅
  P2.5  No existing branch → skip staleness
  P3    Clean tree → checkout -b spec-01-mvp-arkanoid → switch; show summary via anchors
  P3.5  New branch → nothing to resume
  P4    Per step: implement → user reviews → approves → tick [x] → commit (code+tick)
        → next; ends reminding to verify criteria

/spec-impl 02-powerups  (state: Draft)
  P2    "Draft" → ❌ error message; no branch, no code

/spec-impl 01-mvp-arkanoid  (spec has Step 1-2 ticked [x])
  P2.5  Spec unchanged since last branch commit → no warning
  P3    Branch exists → switch (after clean-tree check)
  P3.5  Reads checkboxes: Step 1,2 done → asks to resume from Step 3
  P4    Continues from Step 3
```

Branch creation is controlled by `AutoCreateBranch` in `specs/.spec-config.yml` (default `true`; `false` makes Phase 3 ask `[y/N]`). Collisions and staleness are guarded by checking git branches for the spec number and comparing spec-file vs. branch commit dates (Phase 2.5 and `/spec` Phase 4).
