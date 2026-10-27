---
name: asset-compression
description: How to compress 3D assets for the web in this project, covering Draco versus Meshopt geometry compression, KTX2 (ETC1S and UASTC) and WebP textures, texture resizing, environment maps, server compression and checking visual quality. Use when reducing download size or GPU memory of models, textures or environment maps.
user-invocable: false
---

# Asset compression

## Geometry

| Method | Best for | Trade-off |
|---|---|---|
| Draco | Smallest static meshes | Slower to decode; needs a decoder download |
| Meshopt | Fast decoding; animated models; morph targets | Slightly larger than Draco before server compression |
| Quantisation only | Quick win with no decoder | Smaller saving |

Default to Meshopt for animated models and Draco for large static ones. Both decode automatically through drei `useGLTF`.

## Textures

| Format | Best for | Notes |
|---|---|---|
| KTX2 with ETC1S | Base colour, emissive, ORM | Very small; stays compressed in GPU memory |
| KTX2 with UASTC | Normal maps, detailed colour | Higher quality, larger files |
| WebP | Quick wins without KTX tooling | Small download, but decompressed in GPU memory |
| AVIF | Smallest download where supported | Slower to decode |
| JPEG / PNG | Avoid for 3D textures | Large and decompressed in GPU memory |

- KTX2 saves GPU memory as well as download size, which matters most on phones
- Resize first: a texture that covers 300 px on screen does not need 4096 px
- Use power-of-two sizes such as 512, 1024 and 2048

## Commands

Inspect sizes before and after:

```bash
npx @gltf-transform/cli inspect public/models/chair.glb
```

Geometry compression and WebP textures in one step:

```bash
npx @gltf-transform/cli optimize assets/source/chair.glb public/models/chair.glb --compress meshopt --texture-compress webp --texture-size 2048
```

KTX2 textures need KTX-Software installed; check the installed version's help before running:

```bash
npx @gltf-transform/cli etc1s --help
```

## Environment maps

- Use 1K or 2K HDR maps; convert to a compressed format where the loader supports it
- Prefer drei presets during development, then a custom compressed map for production

## Server compression

- Serve `.glb`, `.gltf`, `.js`, `.css` and `.json` with Brotli or gzip
- Do not re-compress `.ktx2`, `.webp` or `.avif`; they are already compressed
- Set the headers described in the static-hosting skill

## Check quality

After compressing, compare the result with the original under the final lighting:
- Look for banding on gradients, blockiness on normal maps and seams on UV edges
- If quality drops noticeably, raise the quality setting or switch the map to UASTC
- Record the before and after file sizes in `docs/performance.md`
