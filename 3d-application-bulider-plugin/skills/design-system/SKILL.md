---
name: design-system
description: How to define the design system for a 3D web application, covering design tokens in tokens.css, layering the UI over the canvas, layout, responsive breakpoints, accessibility and the structure of docs/design-spec.md. Use when designing or styling the 2D interface.
user-invocable: false
---

# Design system

## Design spec structure

Write `docs/design-spec.md` with these sections:

1. **Visual direction**: mood, references and how the 3D scene and UI should feel together
2. **User flows**: one short flow per epic
3. **Layout**: desktop and mobile layouts showing the canvas, HUD and panels
4. **Design tokens**: a summary table pointing to `src/styles/tokens.css`
5. **Components**: each UI component with its states (default, hover, focus, active, disabled, loading)
6. **3D interactions**: see the 3d-interaction-patterns skill
7. **Loading and error states**
8. **Accessibility**

## Tokens

Define every token as a CSS custom property in `src/styles/tokens.css`:

```css
:root {
  /* Colour */
  --color-bg: #0e1116;
  --color-surface: rgba(20, 24, 31, 0.72);
  --color-text: #f2f4f8;
  --color-text-muted: #a3acb9;
  --color-accent: #4f8cff;
  --color-danger: #ff5c5c;
  --color-focus: #ffd166;

  /* Typography */
  --font-sans: "Inter", system-ui, sans-serif;
  --text-sm: 0.875rem;
  --text-md: 1rem;
  --text-lg: 1.25rem;
  --text-xl: 1.75rem;

  /* Spacing (4 px scale) */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-6: 24px;
  --space-8: 32px;

  /* Shape and depth */
  --radius-sm: 6px;
  --radius-md: 12px;
  --shadow-panel: 0 8px 24px rgba(0, 0, 0, 0.35);
  --blur-panel: 12px;

  /* Layers */
  --z-canvas: 0;
  --z-hud: 10;
  --z-panel: 20;
  --z-modal: 30;
  --z-toast: 40;

  /* Motion */
  --duration-fast: 150ms;
  --duration-normal: 300ms;
  --ease-standard: cubic-bezier(0.2, 0, 0, 1);
}

@media (prefers-reduced-motion: reduce) {
  :root {
    --duration-fast: 0ms;
    --duration-normal: 0ms;
  }
}
```

Developers must use tokens, never raw values. When you add a token, add it to the summary table in the design spec too.

## UI over a 3D canvas

- The canvas fills the viewport at `--z-canvas`; the UI sits above it
- The UI layer uses `pointer-events: none`, and each interactive element sets `pointer-events: auto`, so empty space still reaches the canvas
- Keep the centre of the screen clear; place HUD elements at the edges
- Use translucent panels with a backdrop blur so the scene stays visible
- Check text contrast against the busiest part of the scene, not a flat background

## Responsive layout

| Breakpoint | Width | Layout |
|---|---|---|
| Mobile | under 640 px | Full-screen canvas; panels become bottom sheets |
| Tablet | 640 to 1024 px | Side panel collapses to an icon rail |
| Desktop | over 1024 px | Side panel open beside the canvas |

Touch targets must be at least 44 by 44 px.

## Accessibility

- Text contrast of at least 4.5:1, and 3:1 for large text and UI controls
- A visible focus outline on every interactive element, using `--color-focus`
- All UI reachable and usable with the keyboard, in a logical tab order
- Respect `prefers-reduced-motion` for UI and camera animation
- Provide a text description of the scene's content for screen readers
