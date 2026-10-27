---
name: pbr-materials
description: How to build physically based materials and lighting in three.js for this project, covering standard and physical materials, texture maps and colour spaces, environment maps, tone mapping and realistic values. Use when creating or adjusting materials, textures or scene lighting.
user-invocable: false
---

# PBR materials

## Choose the material

| Material | Use for | Cost |
|---|---|---|
| `MeshBasicMaterial` | Unlit UI-like objects, backgrounds | Lowest |
| `MeshLambertMaterial` / `MeshMatcapMaterial` | Stylised or low-end devices | Low |
| `MeshStandardMaterial` | Most real-world surfaces | Medium |
| `MeshPhysicalMaterial` | Glass, clear coat, sheen fabric, iridescence | High; use sparingly |

## Texture maps and colour spaces

| Map | Property | Colour space |
|---|---|---|
| Base colour (albedo) | `map` | sRGB |
| Emissive | `emissiveMap` | sRGB |
| Normal | `normalMap` | Linear |
| Roughness / metalness / occlusion | `roughnessMap`, `metalnessMap`, `aoMap` | Linear |

- Textures loaded from glTF files get the right colour space automatically
- For textures loaded by hand, set `texture.colorSpace = SRGBColorSpace` on colour maps only
- Pack occlusion, roughness and metalness into one ORM texture (red, green, blue) to save memory and requests

## Realistic values

| Surface | Metalness | Roughness |
|---|---|---|
| Polished metal | 1.0 | 0.05 to 0.2 |
| Brushed metal | 1.0 | 0.3 to 0.5 |
| Glossy plastic | 0.0 | 0.1 to 0.3 |
| Matte plastic | 0.0 | 0.5 to 0.8 |
| Wood | 0.0 | 0.4 to 0.8 |
| Fabric | 0.0 | 0.7 to 1.0 |

- Metalness should be 0 or 1 for almost every real surface; values in between are for transitions such as dirt on metal
- Base colours for non-metals should stay roughly between 30 and 240 in sRGB; pure black and pure white do not exist in nature

## Environment lighting

- Use an HDR environment map for reflections and ambient light; PBR looks flat without one
- Start with drei `<Environment preset="studio" />`, then switch to a custom HDR in `public/env/`
- Keep environment maps at 1K or 2K resolution; 4K is rarely worth the download
- Use `background={false}` unless the design wants the environment visible behind the scene

## Tone mapping and exposure

- React Three Fiber uses ACES Filmic tone mapping by default; adjust `gl.toneMappingExposure` rather than brightening every light
- Judge materials under the final lighting, not in isolation

## Checklist before handoff

- [ ] Every colour map is sRGB and every data map is linear
- [ ] Materials look correct under the chosen environment map
- [ ] No material uses `MeshPhysicalMaterial` features it does not need
- [ ] Texture sizes are within the budgets in the gltf-asset-pipeline skill
