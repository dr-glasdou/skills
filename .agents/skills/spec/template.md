# Template for a useful spec

Reference the `/spec` skill consults when generating specs. This is the **shape** to respect, not text to copy verbatim.

**Every section carries an `<!-- section:xxx -->` comment after its heading.** These are not decoration: `/spec-impl` parses specs by these anchors instead of guessing section boundaries from heading text in whatever language the spec uses. Always include them, in English, even in a non-English spec. They're invisible when rendered.

---

## Header

Metadata in a blockquote (no tables):

```markdown
# SPEC NN — Short, descriptive title

<!-- section:header -->
> **Status:** Draft
> **Depends on:** SPEC 01, SPEC 02
> **Date:** YYYY-MM-DD
> **Objective:** A single sentence. If you need two, the feature is too big.
```

**Valid states:** `Draft`, `In review`, `Approved`, `Implemented`, `Obsolete` — or their equivalents in any language (Spanish: `Borrador` / `En revisión` / `Aprobado` / `Implementado` / `Obsoleto`). Pick one set per repo, stay consistent.

**Hard positional rule:** `Status` is always the **first line** of the blockquote, in every language. `/spec-impl` reads line 1 directly rather than searching for a label — this is what makes state detection reliable across languages.

**Objective rule:** one sentence a human understands in 5 seconds. If it doesn't fit, split the feature.

---

## Section 1 — Why this spec exists (optional)

```markdown
<!-- section:rationale -->
## Why this spec exists
```

For specs with non-obvious decisions or that break project patterns: a brief **why** (not the what). Omit entirely for simple specs (including the anchor).

---

## Section 2 — Scope

Two sub-blocks, **both mandatory:**

```markdown
<!-- section:scope -->
## Scope

**In:**

- Concrete thing one.
- Concrete thing two.

**Out of scope (for future specs):**

- Something possible but not now.
- Something raised in conversation but deferred.
```

The "out" block records what was mentioned and deferred during questioning — without it, implementation is tempted to slip those in "while we're at it".

---

## Section 3 — Data model

Concrete structures, real code, not abstract pseudocode.

```markdown
<!-- section:data-model -->
## Data model

\`\`\`js
const state = {
level: 1,
score: 0,
highScores: [/* { score, level, date } */],
};
\`\`\`

Conventions:

- Coordinates: origin top-left.
- Velocities in pixels/frame.
```

If no new data, keep the anchor and say so explicitly: _"No new data structures. Reuses the model from SPEC 01."_

---

## Section 4 — Implementation plan

Numbered steps. Each leaves the system **functional and runnable** — no "implement half".

```markdown
<!-- section:plan -->
## Implementation plan

- [ ] **Step 1** — Create file X with an empty skeleton.
- [ ] **Step 2** — Implement function A in X. Manual test: run Y, see Z.
- [ ] **Step 3** — Wire X to existing module W.
```

Rules: each step commitable on its own; split anything >30–50 lines; the last step is not "test everything" (that's the acceptance criteria). `/spec-impl` ticks each box (`[ ]` → `[x]`) and commits it with the step **after you approve** — keep step numbers stable once approved, since resume reads these boxes.

---

## Section 5 — Acceptance criteria

Boolean checklist, each verifiable yes/no.

```markdown
<!-- section:acceptance -->
## Acceptance criteria

- [ ] The game loads without console errors.
- [ ] Breaking a brick adds exactly 10 points.
- [ ] Reloading preserves the high-scores.
```

Avoid: "works well" (not verifiable), "good UX" (subjective), "no bugs" (not operational). Prefer: "Esc pauses and shows the menu" (verifiable, boolean).

---

## Section 6 — Decisions taken and discarded

Highest-value section 3 months out. Capture what you **considered**, not just what you chose.

```markdown
<!-- section:decisions -->
## Decisions

- **Yes:** localStorage. Fits <5MB, no queries needed.
- **No:** IndexedDB. Overengineering here.
- **Yes:** versioned key (`save:v1`). Lets us migrate later.
- **No:** cloud sync. Another spec if it lands.
```

Each decision gets a brief reason — reasonless decisions are the first questioned later.

---

## Section 7 — Identified risks (optional)

Only for non-obvious risks:

```markdown
<!-- section:risks -->
## Risks

| Risk                                  | Mitigation                                                      |
| ------------------------------------- | -------------------------------------------------------------- |
| localStorage disabled in private mode | Fallback to in-memory object. Runs, just doesn't persist.      |
| Future incompatible schema            | Key includes `:v1`. Migration documented in `persistence.js`.  |
```

Omit entirely (with anchor) for small/contained specs.

---

## Final section — What is NOT in (reinforcement)

Repeat at the end what won't be done. Deliberate redundancy — a reminder for anyone reading only the last lines.

```markdown
<!-- section:not-in-scope -->
## What is **not** in this spec

- Visual editor (another spec if it lands).
- Multiplayer.
- Mobile version.

Each, if it lands, goes in its own spec.
```

---

## Global rules

- One idea per sentence. Two commas and a semicolon → split it.
- Concrete names: `src/levels.js`, not "the levels module"; the exact key string, not "a key".
- No TODOs. A TODO means an unmade decision — make it, or note it as pending with a reason.
- No long executable code. Short snippets for data structures are fine; full functions aren't.
- Standard markdown, plus the `<!-- section:xxx -->` anchors — otherwise renders on GitHub without surprises.
