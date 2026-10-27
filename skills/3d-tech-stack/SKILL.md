---
name: 3d-tech-stack
description: The default technology stack for 3D web applications built with this plugin (Vite, React, TypeScript, React Three Fiber, drei, Zustand), when to deviate from it, and how to record decisions as ADRs. Use when choosing or changing the stack or adding a dependency.
user-invocable: false
---

# 3D web application tech stack

## Default stack

Start from this stack unless a requirement gives a clear reason to deviate.

| Concern | Default | Notes |
|---|---|---|
| Build tool | Vite | Fast dev server; native ES modules |
| Language | TypeScript, `strict: true` | |
| UI framework | React | |
| 3D renderer | three.js through `@react-three/fiber` | Declarative scene graph in React |
| 3D helpers | `@react-three/drei` | Controls, loaders, `Environment`, `Html`, `useGLTF` |
| Post-processing | `@react-three/postprocessing` | Only if the design needs effects |
| Physics | `@react-three/rapier` | Only if the requirements need physics |
| State | `zustand` | Shared between UI and scene |
| Debug panel | `leva` | Development builds only |
| Performance overlay | `r3f-perf` | Development builds only |
| Unit tests | Vitest | |
| End-to-end tests | Playwright | |
| Lint and format | ESLint and Prettier | |
| Backend (if needed) | Node.js with Fastify, or a managed backend | Decide from the requirements |

## Versions

Do not pin versions from memory. Install the current releases when scaffolding:

```bash
npm create vite@latest . -- --template react-ts
npm install three @react-three/fiber @react-three/drei zustand
npm install -D @types/three vitest @playwright/test
```

Check that the installed `@react-three/fiber` major version supports the installed React major version, and record the versions in `docs/architecture.md`.

## When to deviate

| Situation | Consider |
|---|---|
| Game-like app with heavy physics, GUI and asset tooling | Babylon.js |
| A single small effect on an otherwise static site | Plain three.js without React |
| Mostly static 3D with little interaction, built by designers | A no-code tool export, embedded |
| Need for compute shaders or very large scenes, with modern browser support only | three.js WebGPU renderer |
| Content-heavy site with SEO needs | A framework with server rendering, with the canvas loaded on the client only |

Any deviation needs an ADR.

## Architecture decision records

Write each significant decision as `docs/decisions/NNNN-short-title.md`:

```markdown
# 0001: Use React Three Fiber for the 3D scene

## Status
Accepted

## Context
<what forces the decision>

## Decision
<what we chose>

## Consequences
<what gets easier, what gets harder>
```

## Adding a dependency

Before adding any package, check:
- Is it actively maintained, with a release in the last year?
- What does it add to the production bundle?
- Is there already a drei helper that does the same thing?
