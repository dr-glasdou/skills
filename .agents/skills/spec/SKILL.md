---
name: spec
description: Designs and develops specs following the spec-driven method. Asks clarifying questions before proposing structure, and builds the spec section by section. Use ONLY when the user explicitly invokes `/spec` to start a large feature, before writing code. Do not trigger on its own.
---

# /spec — Guided spec designer

Help the user clarify what they want to build and develop the spec section by section until it is ready to save into `specs/`. **You don't write code here.** A spec is the contract that drives later execution: vague spec → improvised code. This flow is deliberately slow during definition and fast during writing.

Read `template.md` (same directory as this skill) for the full structure the spec follows. Lean on it at every step.

Reply in the same language as the initial prompt. Follow the four phases in order — **do not skip phases**. If the user wants to go faster, remind them the cost of a bad spec gets paid later in code.

### Phase 1 — Understand the context

1. Read the project-memory file, first hit wins: `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `README.md`.
2. List `specs/` to see existing specs and numbering.
3. If previous specs exist, read the two most recent to pick up conventions.

If `$ARGUMENTS` is empty, ask for a **single-sentence** description of the feature. If it doesn't fit in one sentence, that's the first signal it's too big — suggest splitting before continuing.

### Phase 2 — Clarify through questions

The most important phase. **Detect ambiguities and ask — never assume.** Ask in blocks of 3–5 questions (not one at a time), then wait for answers.

**Categories to always consider:** scope (in / out / deferred), data (new structures, names, location), integration (dependencies on prior specs, modifies vs. adds), persistence (what, where, versioning), UX and states (success, failure, intermediate), risks (what breaks it, degraded case), closed decisions (already made, not to reopen).

**Phrasing:**

- Concrete, not open-ended. ❌ "How do you imagine persistence?" → ✅ "localStorage, IndexedDB, or a JSON file on disk?"
- When offering options, give 2–4 and mark your recommendation with a reason.
- If an answer opens Pandora's box (e.g. "and also multiplayer"), point out it deserves its own spec and ask whether to leave it out of scope.

**Stop asking** when you can answer these three without assuming: (1) which files will appear or change, (2) what is the first executable step and the last, (3) how to verify the feature is finished. Otherwise, keep asking.

### Phase 3 — Develop the spec section by section

**Never generate the full spec in one shot.** Develop the template's sections one by one, in this strict order, showing each and waiting for confirmation before the next:

1. **Header** (state, dependencies, date, one-sentence objective — if it doesn't fit in one sentence, back to Phase 2).
2. **Scope** (in AND explicit not-in).
3. **Data model** (concrete structures, real names; if no new data, skip and say so explicitly).
4. **Implementation plan** (numbered steps, each leaving the system functional).
5. **Acceptance criteria** (boolean checklist, not aspirational).
6. **Decisions taken and discarded** (with brief justification).
7. **Identified risks** (only if applicable).

After each section: show it in markdown, ask "Does this section stay like this or do you want to tweak it?", apply requested changes, and only advance once confirmed.

**Avoid:** non-verifiable criteria ("that it works well"), plan items outside the scope, assuming unconfirmed file names or structures, skipping the decisions section (the one with the most long-term value).

### Phase 4 — Save the spec

When all sections are confirmed:

1. Next sequential number from `specs/` (last is `02-powerups.md` → this one is `03-`).
2. Short slug from the objective (e.g. `levels-and-highscores`).
3. Confirm the proposed file name with the user before writing.
4. Create `specs/NN-slug.md` with all approved sections, state `Draft`. **Never mark `Approved` automatically** — the user does that after re-reading.
5. **Seed the config if missing.** If `specs/.spec-config.yml` does not exist, create it with the content below. If it exists, **leave it untouched**.

   ```yaml
   # spec workflow configuration
   #
   # AutoCreateBranch — controls whether /spec-impl creates the git branch automatically.
   #   true  (default) → /spec-impl creates and switches to spec-NN-slug without asking
   #   false           → /spec-impl asks for [y/N] confirmation before creating the branch
   AutoCreateBranch: true
   ```

6. Confirm: path of the created file; reminder it's in `Draft` — change to `Approved` after re-reading; if you just created the config, mention it and its default; next step is `/spec-impl NN-slug`. **Stop here** — do not propose implementing or any further action.

## Hard rules

- **Never write code.** Only the spec's `.md` file at the end.
- **Never propose implementing after saving.** Your job ends when the file is written.
- **Never assume unconfirmed decisions.** Missing info → ask.
- **Never generate the full spec in a single response.**
- **If the user wants to skip Phase 2**, remind them: "Questions now save hours later. Are you sure?". If they insist, respect it but record it in the decisions section ("Quick definition without detailed clarification").
- **If the feature is too big** (doesn't fit in one sentence, touches 3+ areas, needs decisions in 4+ domains), propose splitting into multiple specs before continuing.

## Tone when asking

Direct and specific. Never apologize for asking or hedge ("if you don't mind..."). Number the questions so they're easy to answer. Example:

> Before writing the data model I need to clarify three things:
>
> 1. **Persistence.** localStorage, IndexedDB, or a JSON file on disk? Recommendation: localStorage if the data fits in <5MB and needs no queries.
> 2. **Schema versioning.** When the format changes: (a) version prefix in the key, (b) ignore and rebuild, (c) migrate on load?
> 3. **Privacy.** Is the data sensitive? If yes, encrypted? Deleted on logout?

## Arguments

- `/spec levels-and-highscores` → use `${ARGUMENTS}` (`levels-and-highscores`) as the initial slug suggestion, but confirm before writing the file.
- `/spec` with no arguments → `${ARGUMENTS}` is empty; start by asking for the one-sentence description.
- Argument matches an existing file in `specs/` (number, slug, or full name) → **resume mode**: read that spec, show its current sections, and continue from the first incomplete or unconfirmed section using the Phase 3 flow. Do not renumber or create a new file.
