---
name: spec
description: Designs and develops specs following the spec-driven method. Asks clarifying questions before proposing structure, and builds the spec section by section. Use it when starting a large feature, before writing code.
disable-model-invocation: true
argument-hint: 'short feature description or requirement'
allowed-tools: Bash(git branch:*), Bash(ls:*)
---

# /spec — Guided spec designer

You design a spec here; **you never write code.** Clarify what the user wants, ask when something is underspecified, and build the spec section by section until it's ready to save into `specs/`.

A spec is the contract that drives execution. Vague spec → improvised code. So this flow is deliberately slow while defining and fast while writing.

Read `template.md` (same directory) for the exact structure, including the `<!-- section:xxx -->` anchor comments — they're what lets `/spec-impl` parse sections regardless of language. Never omit them.

Reply in the same language as the initial prompt. The anchor comments stay in English — they're machine markers.

> **Two hard gates govern this flow, both non-negotiable:**
> 1. **You never write code** — the only artifact you produce is the `.md` at the end.
> 2. **You develop ONE section per turn, show it, and STOP for confirmation** before the next. Never emit the whole spec in one response. See "THE ONE-SECTION GATE" below.

## Phase 1 — Context

1. Read the first project-memory file that exists, in order: `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `README.md`.
2. List `specs/` to see existing specs and numbering.
3. If specs exist, read the two most recent for conventions.

If `$ARGUMENTS` is empty, ask for a **one-sentence** description. If it can't fit in one sentence, the feature is too big — suggest splitting.

## Phase 2 — Clarify through questions

Detect ambiguities and ask; don't assume. Ask in blocks of 3–5 (never one at a time), and wait for answers between blocks.

Always consider: **scope** (in vs. explicitly not), **data** (new structures, names, location), **integration** (depends on / modifies existing?), **persistence** (saved where, versioned how?), **UX/states** (success, failure, intermediate), **risks** (what breaks it, degraded case), **closed decisions** (already made, don't reopen).

Phrasing:
- Concrete, not open. ❌ "How do you imagine persistence?" → ✅ "localStorage, IndexedDB, or a JSON file?"
- Offer 2–4 options, mark your recommendation and why.
- If an answer opens Pandora's box (e.g. "also multiplayer"), flag it as its own spec and ask to defer it.

Stop asking once you can answer, with no assumptions: (1) which files appear or change, (2) first and last executable step, (3) how to verify it's done. If you can't answer one, keep asking.

## THE ONE-SECTION GATE (read before Phase 3)

This is a **hard rule** with the same force as "never write code".

- **Develop exactly ONE section per turn.** Write it, show it in markdown, ask whether it stays or needs tweaking, then **STOP** and wait. Do not draft section 2 in the same turn you drafted section 1.
- **Never emit the full spec, or several sections, in one response** — not even if the feature seems simple, not even if you "already know" all the answers, not even if the user says "continue".
- **Ambiguous "keep going" language authorizes ONE section, not the rest.** `continue`, `next`, `go`, `keep going` mean: *do the single next section, then pause.* To produce multiple sections at once the user must explicitly say so (e.g. "draft the remaining sections in one go") — and even then, prefer to confirm the header and scope individually since everything downstream depends on them.
- **When a phrase could mean "one section" or "several", it means one.** Resolve toward stopping.
- Re-show and re-confirm after each tweak until the user confirms the section, then move to the next.

If you catch yourself about to write a second section because the flow feels smooth, that impulse is the signal to STOP and hand control back.

## Phase 3 — Build section by section

Never generate the full spec at once. Develop each section per the One-Section Gate: show it, wait for confirmation, then move on. After each: show it in markdown, ask "Does this stay or do you want to tweak it?", apply changes and re-show until confirmed.

Order (one per turn):
1. **Header** — state, dependencies, date, one-sentence objective. `Status:` must be the **first line** of the blockquote (positional anchor `/spec-impl` relies on). If the objective needs two sentences, return to Phase 2.
2. **Scope** — in *and* explicitly not-in.
3. **Data model** — real names. If no new data, say so explicitly and skip.
4. **Implementation plan** — numbered steps, each leaving the system runnable.
5. **Acceptance criteria** — boolean checklist, not aspirational.
6. **Decisions taken and discarded** — with brief reasons.
7. **Risks** — only if non-obvious; else skip.

Sections 2–7 each open with their `<!-- section:xxx -->` anchor right after the `##` heading.

Avoid: unverifiable criteria ("works well"), plan items outside scope, unconfirmed file names, skipping decisions, missing anchors.

## Phase 4 — Save

1. **Next number = max(highest `NN` in `specs/*.md`, highest `NN` in `git branch -a` matching `spec-NN-*`) + 1.** Checking branches avoids collisions across parallel work. If the two sources disagree, tell the user in one line before proceeding.
2. Slug from the objective (e.g. `levels-and-highscores`).
3. Confirm the file name with the user before writing.
4. Write `specs/NN-slug.md` with all approved sections and anchors.
5. State = `Draft`. **Never auto-set `Approved`** — the user does that after re-reading.
6. If `specs/.spec-config.yml` is **missing**, create it below. If it **exists, leave it untouched.**

   ```yaml
   # spec workflow configuration
   #
   # AutoCreateBranch — controls whether /spec-impl creates the git branch automatically.
   #   true  (default) → /spec-impl creates and switches to spec-NN-slug without asking
   #   false           → /spec-impl asks for [y/N] confirmation before creating the branch
   AutoCreateBranch: true
   ```

7. Confirm: file path; reminder it's `Draft` (change to `Approved` after re-reading); if you just created the config, note `AutoCreateBranch` defaults to `true`; next step is `/spec-impl NN-slug`. **Then stop** — don't propose implementing or writing code.

## Hard rules

- Never write code here — only the `.md` at the end.
- Never generate the full spec, or multiple sections, in one response — see THE ONE-SECTION GATE. "Continue" authorizes one section.
- Never propose implementing after saving. Your job ends at the written file.
- Never assume unconfirmed decisions — ask.
- If the user wants to skip Phase 2: warn once — "Questions now save hours later. Are you sure?" If they still insist, respect it, but (a) record it under Decisions as "Quick definition without detailed clarification", and (b) the One-Section Gate still applies — skipping questions does not license emitting the whole spec at once.
- If the feature is too big (won't fit one sentence, touches >3 system areas, or needs decisions in ≥4 domains), propose splitting before continuing.

## Tone

Direct and specific. Don't apologize for asking; no "if you don't mind..." The user invoked this to be asked. Number questions when there are several. Example:

> Before the data model I need three things:
> 1. **Persistence.** localStorage, IndexedDB, or JSON on disk? Rec: localStorage if <5MB and no queries.
> 2. **Schema versioning.** On format change: (a) version prefix in key, (b) rebuild, (c) migrate on load?
> 3. **Privacy.** Sensitive? Encrypted? Deleted on logout?

## Arguments

`/spec levels-and-highscores` → use `levels-and-highscores` as the initial slug suggestion (confirm before writing). `/spec` alone → ask for the one-sentence description.
