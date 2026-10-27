---
name: 3d-interaction-patterns
description: Proven interaction patterns for 3D web applications, covering camera controls, selection and hover, annotations, camera transitions, scroll-driven scenes, touch gestures, loading states and accessible alternatives, plus how to specify them. Use when designing or implementing how users interact with a 3D scene.
user-invocable: false
---

# 3D interaction patterns

## Specify every interaction like this

```markdown
### Interaction: Select a part
- Trigger: click or tap on a part; Enter on the focused part in keyboard mode
- Feedback: outline highlight within 100 ms; details panel opens
- Camera: eases to frame the part over 600 ms (instant with reduced motion)
- Exit: click empty space, press Escape, or close the panel
- Touch: same as click; no hover state
- Keyboard: Tab cycles through parts; Enter selects; Escape clears
```

## Camera controls

| Pattern | Use when | drei helper |
|---|---|---|
| Orbit around a target | Viewing a single object or product | `OrbitControls` |
| Constrained orbit | Product viewer; stop users getting lost | `OrbitControls` with min/max polar angle and distance |
| Smooth damped camera | Polished feel; preset views | `CameraControls` |
| Presentation (drag rotates object, camera fixed) | Simple product spin | `PresentationControls` |
| Scroll-driven camera | Storytelling and landing pages | `ScrollControls` with `useScroll` |
| First-person | Walkthroughs and spaces | `PointerLockControls` or `KeyboardControls` |

Defaults:
- Always limit zoom distance so the object cannot be lost or clipped
- Disable panning unless users need it
- Provide a **Reset view** button

## Selection and hover

- Hover: change the cursor to a pointer and add a subtle highlight (outline or emissive tint)
- Select: a stronger highlight plus a details panel in the UI overlay
- Touch devices have no hover, so never hide essential information behind hover
- Stop event propagation so clicks select only the nearest object

## Annotations and labels

- Use drei `Html` for a small number of labels anchored to 3D points
- Hide labels that are behind the model with the `occlude` option
- For more than about 20 labels, render them as sprites or text in the scene instead of DOM elements

## Transitions

- Animate the camera with easing between 400 and 900 ms
- Never move the camera without the user causing it, apart from an intro that can be skipped
- With `prefers-reduced-motion`, jump straight to the end state

## Loading states

- Show a loading screen with real progress (drei `useProgress`)
- Show the UI shell immediately; load heavy models after the first view where possible
- If WebGL is unavailable, show a clear message and a static image fallback

## Accessible alternatives

- Keyboard: arrow keys rotate, plus and minus zoom, Tab cycles interactive objects, Escape clears selection
- Screen readers: describe the scene and the selected object in a visually hidden live region
- Every action available in 3D should also be available from the UI overlay, for example a parts list next to clickable parts
