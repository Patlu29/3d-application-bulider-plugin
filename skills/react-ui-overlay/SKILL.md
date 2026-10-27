---
name: react-ui-overlay
description: How to build the React DOM interface that sits over a React Three Fiber canvas in this project, covering layering and pointer events, the loading screen, in-scene labels, component conventions, styling with tokens and accessibility. Use when building HUDs, panels, menus or other UI around the 3D scene.
user-invocable: false
---

# React UI overlay

## Layering

```tsx
export function App() {
  return (
    <div className="app">
      <Experience />          {/* canvas, fills the viewport */}
      <div className="ui-layer">
        <Hud />
        <SidePanel />
      </div>
      <LoadingScreen />
    </div>
  );
}
```

```css
.app { position: fixed; inset: 0; }
.app canvas { position: absolute; inset: 0; z-index: var(--z-canvas); }
.ui-layer { position: absolute; inset: 0; z-index: var(--z-hud); pointer-events: none; }
.ui-layer > * { pointer-events: auto; }
```

- The UI layer passes clicks through to the canvas except where there is a control
- Keep UI components outside `<Canvas>`; components inside it render to WebGL, not the DOM

## Loading screen

```tsx
import { useProgress } from "@react-three/drei";

export function LoadingScreen() {
  const { active, progress } = useProgress();
  if (!active) return null;
  return (
    <div className="loading" role="status" aria-live="polite">
      Loading {Math.round(progress)}%
    </div>
  );
}
```

- `useProgress` works outside the canvas
- Fade the loading screen out rather than removing it abruptly, unless the user prefers reduced motion
- Show a WebGL-unavailable message when the canvas cannot be created

## Labels inside the scene

Labels anchored to 3D points use drei `Html` inside the canvas. Those belong to the scene developer. Your job is to provide the shared label component and styles in `src/ui/components/` for them to use.

## Talking to the scene

- UI actions write to the zustand stores in `src/state/`; the scene reads from them
- Never reach into three.js objects from the UI
- See the state-management skill for store conventions

## Components

- One component per file in `src/ui/components/`, full screens in `src/ui/screens/`
- Style with CSS modules or plain CSS using the tokens in `src/styles/tokens.css`; never hard-code colours, spacing or fonts
- Build every state from the design spec: default, hover, focus, active, disabled and loading

## Accessibility

- Use real elements: `<button>` for actions, `<a>` for navigation, `<dialog>` for modals
- Every icon-only button has an `aria-label`
- Manage focus when panels and dialogs open and close
- Announce scene changes, such as the selected object, in a visually hidden `aria-live` region
- Test the whole UI with the keyboard only
