# Skills

Reusable agent skills for spec-driven development, semantic Git commits, and lean agent instructions.

[![skills.sh](https://skills.sh/b/dr-glasdou/skills)](https://skills.sh/dr-glasdou/skills)

## Install

```sh
npx skills add dr-glasdou/skills
```

Install one skill:

```sh
npx skills add dr-glasdou/skills --skill semantic-commit
npx skills add dr-glasdou/skills --skill spec
npx skills add dr-glasdou/skills --skill spec-impl
npx skills add dr-glasdou/skills --skill slim-agents-md
```

## Included skills

- `semantic-commit` — groups changes into semantic commits and optionally pushes them.
- `spec` — creates implementation-ready specs through guided clarification.
- `spec-impl` — implements approved specs step by step on a dedicated branch.
- `slim-agents-md` — rewrites `AGENTS.md` / similar always-on files into short conventions to save context tokens.
- `user-story-builder` — structured user stories with acceptance criteria and tasks (`user-history/`).
- `yaak-test-logging` — persists Yaak MCP HTTP exchanges to auditable log files.

## OpenCode

OpenCode loads installed skills on demand via the `skill` tool; it does **not** auto-fetch skills from skills.sh. To use these skills:

```sh
npx skills add dr-glasdou/skills --agent opencode
```

Then restart OpenCode so it picks up the new files under `.agents/skills/`. Invoke a skill with its name (`spec`, `spec-impl`, `semantic-commit`, `slim-agents-md`); arguments are referenced as `$ARGUMENTS` inside the skill body.

## License

MIT — see `LICENSE`.
