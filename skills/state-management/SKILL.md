---
name: state-management
description: How shared state is managed between the React UI and the React Three Fiber scene in this project with zustand, covering store layout, selectors, reading state in the render loop, URL state and server data. Use when creating or changing stores, or connecting UI actions to the 3D scene.
user-invocable: false
---

# State management

## Where state lives

| Kind of state | Where | Example |
|---|---|---|
| Shared between UI and scene | zustand store in `src/state/` | Selected part, chosen colour, camera preset |
| Local to one component | `useState` / `useRef` | Whether a dropdown is open |
| Per-frame values | Refs, never React state | Current rotation, animation time |
| Shareable in a link | URL search params, synced to the store | Chosen configuration |
| From the backend | A data-fetching library, or a store slice with loading and error fields | Saved scenes |

## Store layout

One store per domain, such as `sceneStore.ts` and `uiStore.ts`:

```ts
import { create } from "zustand";

type CameraPreset = "front" | "side" | "top";

interface SceneState {
  selectedPartId: string | null;
  colour: string;
  cameraPreset: CameraPreset;
  selectPart: (id: string | null) => void;
  setColour: (colour: string) => void;
  setCameraPreset: (preset: CameraPreset) => void;
}

export const useSceneStore = create<SceneState>()((set) => ({
  selectedPartId: null,
  colour: "#4f8cff",
  cameraPreset: "front",
  selectPart: (id) => set({ selectedPartId: id }),
  setColour: (colour) => set({ colour }),
  setCameraPreset: (preset) => set({ cameraPreset: preset }),
}));
```

- Keep actions inside the store; components call actions instead of calling `set` directly
- Store plain data only (strings, numbers, IDs); never store three.js objects

## Reading state

- In components, select only what you need so unrelated changes do not re-render:

```ts
const colour = useSceneStore((s) => s.colour);
```

- In `useFrame`, read without subscribing, so the scene does not re-render every change:

```ts
useFrame(() => {
  const { selectedPartId } = useSceneStore.getState();
});
```

- To react to a change outside React, use `useSceneStore.subscribe`

## Documenting the store

Whenever you add or change a store field, list it in your handoff note with its type, its meaning and which agent reads it. The scene developer relies on this list.

## Test hook

In development and test builds only, expose the stores for end-to-end tests:

```ts
if (import.meta.env.DEV || import.meta.env.MODE === "test") {
  (window as unknown as { __APP_TEST__: unknown }).__APP_TEST__ = { useSceneStore };
}
```

This lets the QA engineer check scene state without reading pixels.
