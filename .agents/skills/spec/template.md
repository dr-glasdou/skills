# Template for a useful spec

Reference consulted by the `/spec` skill. **Not text to copy verbatim** — it is the shape the skill must respect.

---

## Header

Metadata in a blockquote (no tables):

```markdown
# SPEC NN — Short, descriptive title

> **Status:** Draft
> **Depends on:** SPEC 01, SPEC 02
> **Date:** YYYY-MM-DD
> **Objective:** A single sentence. If you need two, the feature is too big.
```

**Valid states:** `Draft`, `In review`, `Approved`, `Implemented`, `Obsolete`. Equivalents in any language are accepted (e.g. `Borrador` / `En revisión` / `Aprobado` / `Implementado` / `Obsoleto`) — pick one set per repo and stay consistent.

**Objective rule:** one sentence a human reads in 5 seconds and understands. Doesn't fit → split the feature.

---

## Section 1 — Why this spec exists (optional)

Brief **why** for specs with non-obvious decisions or that break project patterns. Omit for simple specs.

## Section 2 — Scope

Two explicit sub-blocks, **both mandatory**:

```markdown
## Scope

**In:**

- Concrete thing one.

**Out of scope (for future specs):**

- Something that came up but was deferred.
```

The "out" list records what was deferred during questioning — without it, implementation will be tempted to slip things in "while we're at it".

## Section 3 — Data model

Concrete structures that appear or change. Real code, not abstract pseudocode:

```markdown
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

No new data → say it explicitly: _"This feature introduces no new data structures. It reuses the model from SPEC 01."_

## Section 4 — Implementation plan

Numbered steps, each leaving the system **functional and runnable**:

```markdown
## Implementation plan

1. Create file X with an empty skeleton.
2. Implement function A in X. Manual test: run Y, see Z.
3. Wire X to existing module W.
```

Rules: each step commitable on its own; a step needing 30–50+ lines gets split; the last step is **not** "test everything" — that's the acceptance criteria.

## Section 5 — Acceptance criteria

Boolean checklist — each item verifiable yes/no:

```markdown
## Acceptance criteria

- [ ] The game loads without errors in the console.
- [ ] Breaking a brick adds exactly 10 points.
- [ ] Reloading the page preserves the high-scores.
```

Anti-patterns: ❌ "That it works well" (not verifiable) · ❌ "Good UX" (subjective) · ❌ "No bugs" (not operational) · ✅ "Pressing Esc pauses the game and shows the menu".

## Section 6 — Decisions taken and discarded

The most valuable section 3 months from now. Capture **what you considered**, not just what you chose, each with a brief reason:

```markdown
## Decisions

- **Yes:** localStorage. Fits in <5MB, no queries needed.
- **No:** IndexedDB. Overengineering for this case.
- **Yes:** versioned key (`save:v1`). Enables schema migration later.
- **No:** cloud sync. Another spec if it ever lands.
```

## Section 7 — Identified risks (optional)

Only for non-obvious risks. Omit for small/contained specs:

```markdown
## Risks

| Risk                                  | Mitigation                                      |
| ------------------------------------- | ----------------------------------------------- |
| localStorage disabled in private mode | Fallback to in-memory object; just no persist.  |
| Future incompatible schema            | Key includes `:v1`. Migration in `persistence.js`. |
```

## Final section — What is NOT in (reinforcement)

Deliberately repeat at the end what will not be done — a reminder for whoever reads only the last lines:

```markdown
## What is **not** in this spec

- Visual editor (another spec if it ever lands).
- Multiplayer.
- Mobile version.
```

---

## Global rules

- **One sentence per idea.** Two commas and a semicolon → split it.
- **Concrete names.** Not "the levels module" — `src/levels.js`. Not "a key" — the exact string.
- **No TODOs.** A TODO means an unmade decision: make it, or note it as pending with a reason.
- **No long executable code.** Short snippets to illustrate data are fine; full functions are not.
- **Standard markdown.** Must render on GitHub without surprises.
