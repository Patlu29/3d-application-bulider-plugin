---
name: requirements-writing
description: How to write the requirements document for a 3D web application, covering structure, numbered testable requirements, MoSCoW priorities and 3D-specific non-functional targets such as frame rate, load time and device support. Use when creating or updating docs/requirements/requirements.md.
user-invocable: false
---

# Writing requirements

## Document structure

Write `docs/requirements/requirements.md` with these sections, in this order:

1. **Overview**: one paragraph on what the app is and who it is for
2. **Goals**: three to five measurable outcomes
3. **Target users**: who they are, their devices and their context of use
4. **Core 3D experience**: what the user sees and does in the scene, in plain language
5. **Functional requirements**: numbered `FR-1`, `FR-2` and so on
6. **Non-functional requirements**: numbered `NFR-1`, `NFR-2` and so on
7. **Out of scope**: what this release will not do
8. **Assumptions**
9. **Open questions**

## Rules for each requirement

- One behaviour per requirement
- Testable: a tester can tell whether it passes
- Priority using MoSCoW: **Must**, **Should**, **Could**, **Won't (this release)**
- Describe the behaviour, not the implementation

| Weak | Strong |
|---|---|
| The model should look good | FR-4 (Must): The product model renders with PBR materials and soft shadows under studio lighting |
| The app should be fast | NFR-1 (Must): The scene holds 60 FPS on a 2022 mid-range laptop and 30 FPS on a 2022 mid-range phone |
| Users can interact with the model | FR-5 (Must): Users can rotate the model by dragging and zoom with the scroll wheel or a pinch |

## Non-functional requirements every 3D app needs

Always include a target for each of these, even if it is an assumption:

| Area | Default target if the user gives none |
|---|---|
| Frame rate | 60 FPS desktop, 30 FPS mobile |
| Initial load | Interactive within 5 seconds on a 4G connection |
| Total download | Under 10 MB for the first view |
| Browsers | Latest two versions of Chrome, Edge, Safari and Firefox |
| Devices | Desktop and mobile, portrait and landscape |
| WebGL fallback | A clear message, or a static image, when WebGL is unavailable |
| Accessibility | WCAG 2.2 AA for the 2D UI; keyboard alternatives for core 3D actions; respects reduced motion |
| Privacy and security | No personal data collected unless a requirement needs it |

## MVP scoping

- Every **Must** requirement together forms the MVP
- If there are more than about 10 Must requirements, challenge each one and move some to Should
- Put anything needing a backend (accounts, saving, uploads) in its own epic so the architect can decide whether a backend is needed
