---
name: webgl-performance
description: How to measure and improve the performance of a React Three Fiber / WebGL application, covering performance budgets, measurement tools, fixes ordered by impact (draw calls, instancing, LOD, on-demand rendering, adaptive resolution, shadows) and the performance report format. Use when auditing or optimising frame rate, memory or load time.
user-invocable: false
---

# WebGL performance

## Budgets

| Metric | Desktop target | Mobile target |
|---|---|---|
| Frame rate | 60 FPS steady | 30 FPS or more steady |
| Draw calls per frame | Under 100 | Under 50 |
| Triangles on screen | Under 500,000 | Under 150,000 |
| Texture memory | Under 256 MB | Under 128 MB |
| JavaScript bundle (gzip) | Under 500 KB for the first view | Same |
| Time to interactive | Under 3 seconds on broadband | Under 5 seconds on 4G |

Use the targets in the requirements document if they differ.

## Measure first

| Tool | Shows |
|---|---|
| `r3f-perf` (`<Perf />` inside the canvas, development only) | FPS, draw calls, triangles, GPU time, memory |
| `gl.info` from `useThree` | Draw calls, triangles, geometries and textures in memory |
| Chrome DevTools Performance panel, with CPU throttling | Long frames, JavaScript cost, garbage collection |
| Chrome DevTools Network panel, with network throttling | Download size and load order |
| Lighthouse | Load performance and bundle issues |
| `npx vite-bundle-visualizer` | What is inside the JavaScript bundle |

Record every measurement with the device, browser, viewport and throttling used.

## Fixes, in order of typical impact

1. **Reduce draw calls**
   - Use instancing for repeated objects (drei `Instances` / `Merged`, or `InstancedMesh`)
   - Merge static meshes that share a material
   - Reduce the number of materials; share materials between meshes
2. **Render only when needed**
   - Use `frameloop="demand"` for scenes that are idle between interactions, and call `invalidate()` on change
3. **Adapt to the device**
   - Clamp `dpr` to `[1, 2]`, and lower it on weak devices with drei `PerformanceMonitor`
   - Use level of detail with drei `Detailed` for large or distant models
4. **Cheaper lighting and shadows**
   - Bake lighting and shadows into textures where the scene is static
   - Use `ContactShadows` or `BakeShadows` instead of real-time shadow maps
   - Limit real-time lights; prefer an environment map
5. **Lighter assets**
   - Compress meshes and textures (see the asset-compression skill)
   - Resize textures to what is actually visible on screen
6. **Faster JavaScript**
   - No allocations or `setState` in `useFrame`
   - Use drei `Bvh` to speed up raycasting on complex meshes
   - Lazy-load heavy parts of the scene and UI with `React.lazy` and dynamic imports
7. **Free memory**
   - Dispose of manually created geometries, materials, textures and render targets
   - Check that `gl.info.memory` stays flat when navigating back and forth

## Report

Write `docs/performance.md`:

```markdown
# Performance report

## Test setup
Device, browser, viewport, throttling

## Results
| Metric | Budget | Before | After |
|---|---|---|---|

## Changes made
- <change>: <file>, <effect measured>

## Remaining issues
- <issue>, <suggested fix>, <owner>
```
