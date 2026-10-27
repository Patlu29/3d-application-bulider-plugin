---
name: gltf-asset-pipeline
description: How 3D assets flow from source files to optimised GLB files in this project, covering folders, the glTF Transform CLI, gltfjsx typed components, Draco/Meshopt and KTX2 loading, asset budgets and naming. Use when importing, converting, optimising or loading 3D models.
user-invocable: false
---

# glTF asset pipeline

## Flow

```text
assets/source/      (originals: .blend, .fbx, .obj, .glb, large textures)
      |  export / convert to glTF 2.0 binary (.glb)
      v
optimise with glTF Transform
      |
      v
public/models/      (optimised .glb served to the browser)
      |
      v
src/scene/objects/  (typed component, optional, via gltfjsx)
```

- Never edit or delete files in `assets/source/`
- Always ship `.glb` (binary glTF), not `.gltf` with separate files
- Export from Blender with Y-up, metres as units and the origin at the object's base

## Optimising with glTF Transform

Inspect a model first:

```bash
npx @gltf-transform/cli inspect assets/source/chair.glb
```

Optimise it with mesh compression and resized, compressed textures:

```bash
npx @gltf-transform/cli optimize assets/source/chair.glb public/models/chair.glb --compress draco --texture-compress webp --texture-size 2048
```

- `--compress meshopt` is an alternative to Draco; it decodes faster and also compresses animation
- For KTX2 textures, use the `etc1s` (colour maps) and `uastc` (normal maps) commands, which need KTX-Software installed
- CLI options change between versions, so check `npx @gltf-transform/cli optimize --help` before relying on a flag

## Typed components with gltfjsx

```bash
npx gltfjsx public/models/chair.glb --types --output src/scene/objects/Chair.tsx
```

The generated component exposes each node and material, so the scene developer can swap materials or hide parts. Re-run it whenever the model changes.

## Loading compressed models

- drei `useGLTF` decodes Draco and Meshopt by default
- The default Draco decoder loads from a CDN; for offline or strict-CSP deployments, copy the decoder into `public/draco/` and pass that path as the second argument to `useGLTF`
- KTX2 textures need a `KTX2Loader` with a transcoder path; pass it through `useGLTF`'s loader extension callback and call `detectSupport(gl)`

## Budgets per model

| Item | Desktop | Mobile |
|---|---|---|
| Triangles, hero model | 150,000 | 50,000 |
| Triangles, whole scene | 500,000 | 150,000 |
| Texture size | 2048 px | 1024 px |
| Materials per model | 5 or fewer | 3 or fewer |
| GLB file size, hero model | 5 MB | 2 MB |
| First-view download, all assets | 10 MB | 5 MB |

If a model is over budget, decimate it in Blender or with `npx @gltf-transform/cli simplify`, and check that it still looks right.

## Naming

- Files: kebab-case, such as `office-chair.glb`
- Nodes and materials inside the model: clear names such as `Seat`, `Legs`, `Fabric`, never `Cube.001`
- Textures: `<model>-<part>-<map>.<ext>`, such as `chair-fabric-normal.ktx2`

## Record every asset

In your handoff note, list each optimised asset with its source file, output file, file size, triangle count and texture sizes.
