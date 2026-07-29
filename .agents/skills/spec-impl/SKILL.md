---
name: spec-impl
description: Implements an approved spec. Validates the state means "Approved" (any language), creates and switches to a branch named after the spec, then implements step by step with pauses to review diffs. Use ONLY when the user explicitly invokes `/spec-impl`. Do not trigger on its own.
---

# /spec-impl — Implementer of approved specs

## Session context

Read this context first, using the available tools:

1. `git status --short` — repository state.
2. `git branch --show-current` — current branch.
3. `ls specs/ 2>/dev/null || echo "The specs/ folder does not exist"` — available specs.
4. `cat specs/.spec-config.yml 2>/dev/null || echo "AutoCreateBranch: true (default, no config file)"` — branch-creation config.

Report the four results at the top of your reply so the user can see them, then continue.

---

## Instructions

Reply in the same language as the spec's content (or the user's messages if they differ).

Follow the four phases in strict order. **Never advance if the previous phase did not complete correctly.**

### Phase 1 — Identify the spec

Argument received: `${ARGUMENTS}`

- If empty: list the specs above, ask the user for the exact name, and stop.
- If present: find the matching file in `specs/` — accept full name (`01-mvp-arkanoid`), number only (`01`), or slug only (`mvp-arkanoid`). If no match, show the available specs and ask the user to correct it.

### Phase 2 — Validate the spec's state

Read the spec file. Find its status line in the header blockquote near the top (per the spec template, format `> **Status:** Value` — label is usually `Status` / `Estado`, but may be in any language; match by position and meaning, not exact wording).

**Absolute rule:** continue only if the state **means "Approved"** in any language (`Approved`, `Aprobado`, `Aprovado`, `Approuvé`, `Genehmigt`, `Approvato`, …). Anything else — Draft, In review, Implemented, Obsolete, an unrecognized value, or no status line at all — means **stop** and show the error message below. If the status line is missing, also tell the user the file does not follow the expected format. If unsure whether a value means "approved", **do not assume** — ask the user to clarify or update the spec to canonical wording.

**Dependency check:** if the header blockquote lists dependencies (`> **Depends on:**` / `> **Depende de:**` / equivalent), read each referenced spec and verify its state means "Implemented". If any is pending, list them and require explicit confirmation before continuing.

**Standard error message when the state is not Approved:**

```
❌ I cannot implement this spec.

Current state: [STATE FOUND]
I only work with specs whose state means "Approved" (e.g. `Approved`, `Aprobado`,
or the equivalent in another language).

To continue you have two options:
  1. If the spec is ready, open it and change the state to "Approved"
     (or your team's equivalent) manually. That change is made by the
     human, not the agent.
  2. If the spec still needs work, use /spec [name] to resume it.
```

Do not offer alternatives or suggest starting anyway. The block is intentional.

### Phase 3 — Create the git branch and switch to it

0. If `git status --short` above shows uncommitted changes, **stop** and ask the user to commit or stash first. Never switch with a dirty working tree.

1. Branch name = spec filename without extension, prefixed: `01-mvp-arkanoid.md` → `spec-01-mvp-arkanoid`.

2. Read `AutoCreateBranch` from the config above. Missing file, missing key, or unrecognized value → `true`. Only an explicit `false` (any capitalization) disables it.

   - **`true` (default):** proceed without asking. Create the branch with `git checkout -b spec-NN-slug` if it doesn't exist; if it already exists, inform the user (previous work may be resumed) and `git checkout spec-NN-slug`. Confirm the switch succeeded before continuing.
   - **`false`:** ask first:

     ```
     AutoCreateBranch is set to false.
     Create and switch to the branch spec-NN-slug? [y/N]
     ```

     Yes → create/switch as above. No or empty → **do not touch git**; say you will implement on the current branch and wait for explicit confirmation to continue there.

3. Confirm visually:

   ```
   ✅ Ready to implement.

   Spec:   specs/NN-slug.md
   Branch: spec-NN-slug  (active)   (← or the current branch, if none was created)
   State:  Approved   (← echo the actual value found in the spec)
   ```

4. **Do not implement yet.** First show the spec summary: objective, scope, implementation plan (numbered steps), and acceptance criteria. Match section headings by meaning — the spec may be in any language.

### Phase 4 — Implement step by step

After the summary, say:

```
I am going to implement the spec following the implementation plan exactly.
I will pause after each step so you can review the diff.

Shall we start with Step 1?
```

Wait for explicit confirmation. Then, for the whole implementation:

**One rule above all:** implement what the spec says. If something looks suboptimal, note it as an observation but implement what was agreed. Changes to the spec go into the spec, not into the code by surprise.

**Work rhythm per step:**

1. Implement the step.
2. Summarize which files you touched and what you did, plus `git diff --stat`.
3. Say: `Step N completed. Could you review the diff and let me know if I continue with Step N+1?` and wait.
4. Once approved, offer to commit: `git add` the touched files, message `spec-NN: step N — short description`. If declined, continue without committing.

**On ambiguity the spec doesn't resolve:** stop, describe it exactly, present 2–3 concrete options, wait for the decision. Do not improvise.

**On out-of-scope requests:** remind the user it's out of this spec's scope, suggest noting it for the next spec, do not implement it on this branch.

**After the last step:**

```
✅ All steps of the plan are implemented.

Next step: verify the spec's acceptance criteria one by one.
If they all pass, update the spec's state to "Implemented" (or your repo's
equivalent) and make the final commit before merging this branch.
```
