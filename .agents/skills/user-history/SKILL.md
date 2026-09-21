---
name: user-story-builder
description: Create structured user stories in Spanish (or English) following the "Como / Quiero / Para" format with acceptance criteria and tasks. Use this whenever the user asks to write, create, or draft a user story, historia de usuario, feature request, or product requirement — even casually ("create a story about X", "escribe una historia para", "add a feature card") — or when defining work for a sprint. Do not use it for task breakdowns unrelated to user story creation, bug reports without acceptance criteria, or documentation that isn't a user story.
---

# user-story-builder — Structure user stories with acceptance criteria and tasks

Create a complete, well-formed user story (description + acceptance criteria + tasks) ready to paste into Trello, Jira, or your workflow tool.

Optional context: `$ARGUMENTS`

## Inspect

Before drafting: gather what the user has already provided.

- **Explicit input**: Did they give a problem statement, feature request, or rough description?
- **Context cues**: Is there an existing issue, ticket, or conversation they're referencing?
- **Language preference**: Spanish ("Como... Quiero... Para") or English ("As a... I want... So that")?
- **Scope hints**: Do they mention effort (story points), stakeholders, or acceptance rules?

If the user provides only a vague direction ("something about auth"), ask clarifying questions before drafting. If they give solid detail, proceed to Plan.

## Safety

- **Ambiguous requirements**: If acceptance criteria would be interpretable multiple ways, stop and ask for clarification. Never guess at business rules.
- **Breaking changes implied**: If the story would require breaking an existing API or removing a feature, flag it and get confirmation—mark it `[BREAKING]` in the story.
- **Sensitive data**: If the story involves auth, secrets, or user PII, ensure acceptance criteria don't expose them (e.g., "API key never logged raw; only prefix displayed").
- **Effort misalignment**: If the story sounds complex but the user hasn't mentioned effort, suggest a story-point range or ask if it should be split.
- **Missing acceptance criteria**: Never create a story without at least 3-5 acceptance criteria. If the user doesn't provide them, generate reasonable ones and ask for feedback.

## Gathering

If clarity is needed, ask in this order (stop when you have enough):

1. **Role & Goal**: Who is this for, and what do they need to do?
   - "**Como** [rol], **Quiero** [acción], **Para** [beneficio]"
   - Or plain: "This story is for [stakeholder] to [do X] because [reason]"

2. **Scope & Constraints**: Any boundaries?
   - "Only applies to [module/endpoint/flow]?"
   - "Must work with [existing system]?"

3. **Acceptance Criteria**: What does "done" look like?
   - Technical: "API returns 200 with X payload when Y condition"
   - Behavioral: "User sees Z message after clicking button"
   - Integration: "System logs request with [fields]"

4. **Tasks / Subtasks** (if complex):
   - Do they want a task checklist, or should the skill generate one based on the criteria?

## Structure

A complete user story has these sections:

```
## Descripción / Description
**Como** [rol] **Quiero** [acción] **Para** [beneficio]

[Optional: 1–2 sentences of business context or technical motivation]

## Criterios de Aceptación / Acceptance Criteria
1. [Criterion 1 — testable, specific]
2. [Criterion 2]
3. [...]

## Tareas / Tasks
- [ ] [Task 1 — specific, actionable]
- [ ] [Task 2]
- [ ] [...]

[Optional: Effort estimate, related tickets, notes]
```

## Message Style

- **Descripción**: Imperative mood, clear role + action. No jargon without context.
  - ✓ "Como consumidor externo de la API de SAAT BI, Quiero que cada endpoint requiera x-api-key, Para que solo integraciones autorizadas lean telemetría"
  - ✗ "API authentication stuff"

- **Criterios de Aceptación**: Each one is independently testable.
  - ✓ "POST /api/validate devuelve 401 si x-api-key es inválido"
  - ✗ "Make auth work"

- **Tareas**: Concrete, assigned-ish (name or generic @role), with context.
  - ✓ "@douglasramirez17 Crear ApiKeyGuard (lee header, llama Portal, adjunta req.apiKey)"
  - ✗ "Code the thing"

## Language & Format

- **Default**: Spanish ("Como / Quiero / Para"), but detect from user input.
- **Bilingual**: If the user mixes languages, clarify which they prefer; proceed in that language.
- **Markdown**: Format for copy-paste into Trello (description field) or Jira (story body).

## Plan

Show the draft story structure:

- Descripción (1–2 lines)
- Criterios de Aceptación (numbered list)
- Tareas (checklist with assigned person if known)

Ask: "Does this capture it? Anything to add, change, or break apart?"

## Execute

Based on feedback:

1. **No changes** → Proceed to output the final formatted story.
2. **Tweaks** → Edit the relevant section and re-show it.
3. **Major rewrites** → Loop back to Gathering for new clarity.
4. **Split into multiple stories** → If acceptance criteria are too loose or tasks too many, suggest splitting (e.g., "core auth" + "logging" + "edge cases") and draft each separately if the user wants.

## Acceptance Criteria Patterns

Use these as starter templates (not gospel—adapt to the story):

### API/Backend story:

1. Endpoint `[METHOD /path]` returns `[code]` when `[condition]`
2. Payload includes fields: `[field1, field2, ...]`
3. Error case: `[condition]` → `[error code + message]`
4. [Integration]: Request is logged to `[system]` with `[context]`

### Frontend/UI story:

1. User sees `[component]` when `[condition]`
2. Clicking `[button]` triggers `[action]` and shows `[feedback]`
3. `[Input field]` validates `[rule]`; invalid input shows `[error]`
4. `[State change]` persists in `[storage/API]`

### Data/ETL story:

1. `[Source]` is read and mapped to `[schema]`
2. `[Transformation]` applies `[logic]`; output validated against `[spec]`
3. Errors are logged to `[destination]`; `[alerting]` triggers if `[threshold]`

## Tasks Patterns

Use these as starter templates:

### For API stories:

- [ ] Create `[ServiceName]` service with `[method names]`
- [ ] Create guard/middleware `[GuardName]` (validate, call external service, attach to request)
- [ ] Register module in app/main module
- [ ] Write unit tests (N test cases covering happy path + N error cases)
- [ ] Test with Postman/Yaak; document curl example in docs/
- [ ] Update `.env.example` with new env vars

### For Frontend stories:

- [ ] Create component `[ComponentName]`
- [ ] Connect to API endpoint `[endpoint]` with loading/error states
- [ ] Write unit tests for interaction
- [ ] Test responsive design (desktop / tablet / mobile)
- [ ] Add to Storybook if using one

## Summary

Return the final formatted story as a cohesive block, ready to copy-paste into your tool. Include:

- Formatted markdown (###, -, [ ])
- Clean language (no meta-commentary)
- Assignee hints if provided (e.g., @username)
- Effort estimate if discussed

---

**After creating a story, ask:** "Ready to add this to Trello/Jira, or refine further?"
