---
name: team-conventions
description: Shared rules for every agent on the 3D web application team, covering folder ownership, project documents, handoff notes, open questions and the report format. Preloaded into every agent in this plugin.
user-invocable: false
---

# Team conventions

You are one member of a team of specialist agents building a 3D web application. These rules keep the team from overwriting each other's work and make sure context passes cleanly between agents.

## Folder ownership

| Path | Owner |
|---|---|
| `docs/requirements/` | product-manager |
| `docs/architecture.md`, `docs/decisions/`, root config files | architect |
| `docs/design-spec.md`, `src/styles/` | ux-ui-designer |
| `src/scene/` (except shaders and materials) | scene-developer |
| `src/scene/shaders/`, `src/scene/materials/`, `public/models/`, `public/textures/`, `public/env/` | technical-artist |
| `src/ui/`, `src/state/`, `src/hooks/`, `src/App.tsx`, `src/main.tsx` | frontend-developer |
| `server/`, `docs/api.md` | backend-developer |
| `docs/performance.md` | performance-optimiser |
| `tests/`, `playwright.config.ts`, `docs/qa-report.md` | qa-engineer |
| `.github/`, `infra/`, hosting config, `docs/deployment.md` | devops-engineer |
| `assets/source/` | The user. Read only for every agent |

Only edit paths you own. If a path you don't own needs a change, describe the change in your handoff note and name the owner. The performance-optimiser has a documented exception for optimisation changes.

## Project documents

Read the documents your task depends on before you start. Do not rely on memory of earlier work.

| Document | Contains |
|---|---|
| `docs/requirements/requirements.md` | Goals, scope, functional and non-functional requirements |
| `docs/requirements/user-stories.md` | Stories and acceptance criteria |
| `docs/architecture.md` | Stack, structure, data flow |
| `docs/design-spec.md` | Layout, tokens, interactions, accessibility |
| `docs/api.md` | Backend API contract |
| `docs/handoffs/<agent-name>.md` | Each agent's latest handoff note |

## Open questions

You cannot ask the user questions directly. When something is unclear:
1. Make the most reasonable assumption and record it under **Assumptions** in your handoff note.
2. If the assumption could change the outcome significantly, also list it under **Open questions**.

## Handoff note

When you finish, write or overwrite `docs/handoffs/<your-agent-name>.md`:

```markdown
# Handoff: <agent-name>

## Summary
<two or three sentences on what you did>

## Files changed
- path/to/file: what changed

## Decisions
- <decision>: <reason>

## Assumptions
- <assumption>

## Open questions
- <question> (who should answer)

## Requests for other agents
- <owner>: <requested change>
```

## Report format

End every task with this report to the agent that delegated to you:

- **Done:** what was completed
- **Not done:** what was skipped or blocked, and why
- **Files changed:** list of paths
- **Risks:** anything that might break or needs checking
- **Open questions:** anything needing the user's decision

## Writing style

- Use Australian English in documents and UI copy (colour, optimise, behaviour)
- Keep code identifiers in the spelling the library uses (for example `color` in three.js)
- Be concise and specific; prefer tables and checklists over long paragraphs
