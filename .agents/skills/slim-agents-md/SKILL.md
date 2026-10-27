---
name: slim-agents-md
description: Rewrites always-on agent instruction files (AGENTS.md, CLAUDE.md, COPILOT.md, .cursor/rules) into short, stack-agnostic conventions that change agent behavior — cutting catalogs, architecture summaries, command encyclopedias, API inventories, and generic best practices so less context is sent to the model on every turn. Use on any repo (frontend, NestJS, Go, Python, monorepos, etc.) whenever the user asks to slim, shorten, optimize, declutter, or reduce tokens in AGENTS.md / agent instructions; to turn a project summary into conventions; or to improve context budget. Do not use for ordinary README edits, product docs, or code refactors unrelated to agent instruction files.
argument-hint: 'path to AGENTS.md (optional; defaults to nearest AGENTS.md / CLAUDE.md)'
---

# slim-agents-md — Turn agent instructions into lean conventions

Always-on files (`AGENTS.md` and similar) are included on **every** request. Goal: **minimum effective dose** — only instructions that change agent behavior in ways this repo requires.

Prefer **conventions** (do / don't / point-to-file) over **project summaries** (inventories, architecture tours, command encyclopedias).

Works for any stack. Infer the project's language, layout, and runners from the repo — do **not** inject frontend-, Angular-, Nest-, or Go-specific rules unless they already appear in the target file.

Optional target path: `$ARGUMENTS`

Read `checklist.md` (same directory) when classifying ambiguous blocks.

## Inspect

1. Resolve the target file:
   - If `$ARGUMENTS` is a path, use it.
   - Else prefer the first that exists in the workspace root (then cwd): `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `COPILOT.md`.
   - If several exist and the choice is unclear, list them and ask — don't guess.
2. Read the full target file.
3. Skim discoverable sources so you know what to **point at** instead of **copy**: `README*`, build/task runners (`Makefile`, `package.json`, `go.mod`, `pyproject.toml`, `Cargo.toml`, `Taskfile*`, etc.), shared packages/modules, and CI / hooks.

## Safety

- **Edit only the instruction file.** Do not rewrite README, source, or config "while you're at it."
- **Do not invent rules** the original file never stated. You are editing, not authoring policy.
- **Do not add stack conventions from your training data** (naming prefixes, folder layouts, framework defaults) that weren't in the file.
- **Do not move cut content into another always-on file** — that defeats the point.
- **Destructive ops** (clean reinstalls, wiping lockfiles, force-pushes): if the target mentions them, keep an "ask before running" constraint.
- If the entire file is redundant with the repo, say so and ask before deleting it.

## Classify

For every section / paragraph, pick one action:

| Action | When |
|--------|------|
| **Keep** | Tool/process musts the agent would miss; non-obvious project defaults; hard constraints; pointers to canonical sources; external-doc / MCP rules with clear when-to-skip |
| **Rewrite** | Useful rule buried in a catalog — keep the rule, drop the inventory |
| **Cut** | Directory trees, module maps, exhaustive API/type/component catalogs, full command lists, config dumps, README clones, generic "write clean code" advice, duplicate restatements |

**Inclusion test (required):** *Would the agent behave incorrectly without this line?* If no → cut. When unsure → cut.

## Rewrite

- Imperative bullets; one rule per line
- Prefer **pointers** over **copies** (path to the package / module / doc, not a pasted inventory)
- Prefer **defaults / exceptions** over tutorials
- Target roughly **≤ 80–100 lines** unless the user asks otherwise
- No persona preamble ("You are an expert…")
- No empty section headers
- Preserve meaning of kept rules; tighten wording only
- Section names should match **this** repo (e.g. Go / Nest / Python / Frontend) — don't force a UI "Design system" section on a backend project

Suggested shape (omit empty sections; rename to fit the stack):

```markdown
# Conventions

## Language / framework   <!-- only non-obvious do/don't for this stack -->
## Layout                 <!-- pointers to shared packages/modules; reuse-before-add -->
## Tooling                <!-- runners, hooks, docs/MCP when/skip -->
```

## Plan

Show before writing:

1. Target path
2. Approximate before → after size (lines)
3. Cut / keep / rewrite bullets (short; group by reason)
4. The proposed new file content (or a tight outline if huge)

Proceed if unambiguous; ask only on real uncertainty (wrong file, delete-entirely, or a rule that looks like a business decision).

## Execute

1. Write the optimized file to the target path.
2. Do not create parallel `AGENTS.slim.md` unless the user asked for a review-only pass.

## Summary

Report:

- Path updated
- Line count before → after
- Top cuts (what class of noise left)
- What was preserved and why (one short list)
