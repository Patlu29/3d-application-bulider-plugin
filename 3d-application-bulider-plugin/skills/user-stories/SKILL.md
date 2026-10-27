---
name: user-stories
description: How to write user stories and Given/When/Then acceptance criteria for a 3D web application, grouped into epics and linked to requirement IDs. Use when creating or updating docs/requirements/user-stories.md.
user-invocable: false
---

# Writing user stories

## Format

```markdown
## Epic E1: Explore the product in 3D

### US-1: Rotate the model (FR-5, Must)
As a shopper, I want to rotate the product by dragging, so that I can see it from every side.

**Acceptance criteria**
- Given the scene has loaded, when I drag horizontally on the canvas, then the model rotates around its vertical axis
- Given I am on a touch device, when I drag with one finger, then the model rotates the same way
- Given I release the drag, when the model is still moving, then it eases to a stop within one second
- Given I use only the keyboard, when the canvas has focus and I press the arrow keys, then the model rotates
```

## Rules

- Every story links to at least one requirement ID and carries its MoSCoW priority
- Every story has two to six acceptance criteria
- Each criterion is a single Given/When/Then statement a tester can check
- Include at least one criterion for touch, keyboard or reduced motion when the story involves interaction
- Include the loading and error states: what the user sees while assets load, and when they fail
- Follow INVEST: independent, negotiable, valuable, estimable, small and testable

## Epics for a typical 3D web app

Use these as a starting checklist and drop the ones that do not apply:

| Epic | Typical stories |
|---|---|
| Loading and first view | Loading screen with progress, first camera position, WebGL fallback |
| Navigating the scene | Orbit, zoom, pan, reset view, preset camera views |
| Interacting with objects | Hover highlight, select, show details, annotations |
| Configuring | Change colour, material or variant; toggle parts |
| UI overlay | Menus, panels, help, settings |
| Accessibility | Keyboard controls, reduced motion, screen reader text |
| Persistence | Save, share link, load a saved state (needs backend or URL state) |
| Accounts | Sign up, sign in, profile (needs backend) |

## Story map

End the document with a table that orders the stories into releases:

| Release | Stories |
|---|---|
| MVP | US-1, US-2, US-4 |
| Release 2 | US-3, US-6 |
