---
name: project-structure
description: The standard folder layout, naming conventions, path aliases and npm scripts for 3D web applications built with this plugin. Use when scaffolding a project, adding new folders, or deciding where a file belongs.
user-invocable: false
---

# Project structure

## Folder layout

```text
my-3d-app/
├── assets/
│   └── source/            # original models and textures from the user (read only)
├── docs/
│   ├── requirements/
│   ├── decisions/
│   ├── handoffs/
│   ├── architecture.md
│   ├── design-spec.md
│   └── api.md             # only if there is a backend
├── public/
│   ├── models/            # optimised .glb files
│   ├── textures/          # compressed .ktx2 / .webp textures
│   └── env/               # environment maps
├── server/                # only if there is a backend
├── src/
│   ├── scene/
│   │   ├── Experience.tsx # the single <Canvas> and top-level scene
│   │   ├── objects/       # one component per scene object
│   │   ├── lights/
│   │   ├── cameras/
│   │   ├── materials/     # technical artist
│   │   └── shaders/       # technical artist
│   ├── ui/
│   │   ├── components/
│   │   └── screens/
│   ├── state/             # zustand stores
│   ├── hooks/
│   ├── styles/
│   │   └── tokens.css
│   ├── App.tsx
│   └── main.tsx
├── tests/
│   ├── e2e/
│   └── unit/
├── index.html
├── package.json
├── tsconfig.json
└── vite.config.ts
```

## Naming

| Item | Convention | Example |
|---|---|---|
| React components | PascalCase file and export | `ProductModel.tsx` |
| Hooks | camelCase starting with `use` | `useCameraRig.ts` |
| Stores | camelCase ending in `Store` | `sceneStore.ts` |
| Models | kebab-case | `office-chair.glb` |
| Textures | kebab-case with map type | `chair-fabric-basecolor.ktx2` |
| Shaders | kebab-case with stage | `water.vert.glsl`, `water.frag.glsl` |

## Path alias

Configure `@/` to point at `src/` in both `tsconfig.json` and `vite.config.ts`, and import with `@/state/sceneStore` rather than long relative paths.

## npm scripts

Every project must provide these scripts so the pipeline and other agents can rely on them:

| Script | Purpose |
|---|---|
| `dev` | Start the dev server |
| `build` | Type check and build for production |
| `preview` | Serve the production build locally |
| `lint` | Run ESLint |
| `typecheck` | Run `tsc --noEmit` |
| `test` | Run unit tests once |
| `test:e2e` | Run Playwright tests |

## Environment variables

- Client-side variables must start with `VITE_` and must never contain secrets
- List every variable in `.env.example` with a comment and no real value
- Add `.env` and `.env.local` to `.gitignore`
