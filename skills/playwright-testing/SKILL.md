---
name: playwright-testing
description: How to write Playwright end-to-end tests for a WebGL / React Three Fiber application, covering setup, mapping acceptance criteria to tests, testing canvas state through test hooks, console error checks, WebGL in headless browsers, mobile viewports and the QA report format. Use when writing or running end-to-end tests or reporting bugs.
user-invocable: false
---

# Playwright testing for 3D apps

## Setup

```bash
npm install -D @playwright/test
npx playwright install chromium firefox webkit
```

`playwright.config.ts` essentials:

```ts
import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "tests/e2e",
  use: { baseURL: "http://localhost:4173", trace: "on-first-retry" },
  webServer: { command: "npm run build && npm run preview", port: 4173, reuseExistingServer: true },
  projects: [
    { name: "chromium", use: { ...devices["Desktop Chrome"] } },
    { name: "firefox", use: { ...devices["Desktop Firefox"] } },
    { name: "webkit", use: { ...devices["Desktop Safari"] } },
    { name: "mobile", use: { ...devices["Pixel 7"] } },
  ],
});
```

## One test per acceptance criterion

Name each test after its story and criterion, so the QA report can map results back:

```ts
test("US-1 AC-2: dragging rotates the model", async ({ page }) => {
  // ...
});
```

## Testing what is inside the canvas

The DOM cannot see 3D objects, so do not try to find them with selectors. Use these instead:

1. **Test hook state.** The frontend exposes `window.__APP_TEST__` in development and test builds:

```ts
const selected = await page.evaluate(() =>
  (window as any).__APP_TEST__.useSceneStore.getState().selectedPartId
);
expect(selected).toBe("seat");
```

2. **Pointer actions on the canvas** using coordinates relative to the canvas box:

```ts
const canvas = page.locator("canvas");
const box = await canvas.boundingBox();
await page.mouse.move(box!.x + box!.width / 2, box!.y + box!.height / 2);
await page.mouse.down();
await page.mouse.move(box!.x + box!.width / 2 + 200, box!.y + box!.height / 2, { steps: 10 });
await page.mouse.up();
```

3. **UI side effects**, such as the details panel opening after a selection
4. **Screenshots** for appearance (see the visual-regression skill)

If a test needs a hook that does not exist, request it from the frontend developer in your handoff note.

## Always check

- Wait for loading to finish before interacting (the loading screen disappears, or a ready flag in the test hook)
- No console errors or WebGL warnings:

```ts
const errors: string[] = [];
page.on("console", (msg) => { if (msg.type() === "error") errors.push(msg.text()); });
// ... at the end of the test
expect(errors).toEqual([]);
```

- No failed network requests for models or textures
- The WebGL-unavailable fallback appears when WebGL is disabled

## WebGL in headless browsers

Headless browsers usually render WebGL in software, which is slow but works. If the WebGL context fails to start in headless Chromium, try launching with `--use-angle=swiftshader` in the project's `launchOptions.args`. Never use frame rate results from headless runs as performance measurements.

## QA report

Write `docs/qa-report.md`:

```markdown
# QA report

## Summary
Passed X of Y tests across chromium, firefox, webkit and mobile.

## Results by story
| Story | Criterion | chromium | firefox | webkit | mobile |
|---|---|---|---|---|---|

## Bugs
### BUG-1: <title>
- Severity: critical / major / minor
- Owner: <agent name>
- Steps to reproduce: ...
- Expected: ...
- Actual: ...
- Evidence: <trace or screenshot path>
```
