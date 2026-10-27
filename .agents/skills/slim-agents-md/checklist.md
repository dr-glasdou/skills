# Cut / keep checklist

Companion to `slim-agents-md`. Load when a block is ambiguous. Examples below are illustrative across stacks — apply the *pattern*, not the stack.

## Always cut

- Persona / expertise fluff
- Language or framework identity the repo already makes obvious (`go.mod`, `package.json`, `pyproject.toml`, imports)
- File trees and folder tours
- Exhaustive task-runner / script encyclopedias when `make help`, README, or the manifest already lists them
- Pasted config dumps (env var tables, full token/theme dictionaries, OpenAPI endpoint catalogs, protobuf field lists)
- Shared-library / component / DTO API tables (point to the package; agent reads sources)
- Architecture / data-flow essays
- CI / hook narratives already encoded in workflow or hook config files
- Generic quality slogans ("maintainable", "scalable", "follow existing patterns")
- Duplicate sections that restate the same rule

## Usually keep

- "Never run X when task-runner target Y exists"
- "Ask before destructive setup"
- Project footguns: defaults or patterns agents still get wrong *here*
- Required patterns and forbidden ones **with a replacement**
- Enforced quality bars that tooling/reviews actually require (coverage, lint gate, a11y, API compatibility — only if present)
- Where shared code / contracts live + reuse-before-add
- External docs / MCP: when to fetch and when to skip
- Commit or PR standards if hooks or CI enforce them

## Rewrite instead of keep-as-is

| Bloated | Convention form |
|---------|-----------------|
| Full design-token / theme dump | `Tokens live in <path>. Use the project's utilities/tokens — don't hardcode raw values.` |
| Shared UI / DTO / client API table | `Shared code: <path> — reuse before adding. Follow existing naming there.` |
| Long script/target list + hook essay | `Run via <runner> (<help command>). Don't bypass the runner when a target exists.` |
| Same rule restated in 3 sections | One bullet under the relevant section |
| Pasted env/config matrix | `Config: <path or tool>. Required secrets: ask; never invent values.` |

## Done when

- Almost every line fails "agent would do this anyway"
- Inventories replaced by paths
- No stack-specific advice was added that wasn't in the original
- File is short enough that a human would actually maintain it
