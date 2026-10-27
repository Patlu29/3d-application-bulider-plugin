---
name: visual-regression
description: How to run reliable visual regression tests on a 3D scene with Playwright screenshots, covering deterministic rendering, test mode, thresholds, baseline management and running in a consistent environment. Use when adding or updating screenshot tests for the canvas or UI.
user-invocable: false
---

# Visual regression for 3D scenes

3D scenes are hard to screenshot reliably because of animation, timing and GPU differences. Make rendering deterministic first, then compare.

## Make the scene deterministic

Ask the frontend and scene developers for a test mode, enabled by a URL parameter such as `?test=1` in development and test builds only, that:

- Stops all animation and auto-rotation
- Puts the camera at a fixed, named position
- Uses a fixed random seed for anything procedural
- Exposes a ready flag once all assets have loaded and the first frame has rendered

## Writing a screenshot test

```ts
test("US-4: default product view", async ({ page }) => {
  await page.setViewportSize({ width: 1280, height: 800 });
  await page.goto("/?test=1&camera=front");
  await page.waitForFunction(() => (window as any).__APP_TEST__?.ready === true);
  await expect(page).toHaveScreenshot("product-front.png", {
    maxDiffPixelRatio: 0.01,
    animations: "disabled",
  });
});
```

- Use a fixed viewport and a device scale factor of 1
- Screenshot named scene states: each camera preset, each colour variant, selected and unselected
- Screenshot the UI overlay separately from the canvas where possible, with `locator.screenshot()`, so UI changes do not break scene baselines

## Thresholds

- Start with `maxDiffPixelRatio: 0.01` for scenes and a stricter `0` to `0.001` for pure UI
- Loosen a threshold only with a reason recorded in the test

## Baselines

- Commit baselines next to the tests; Playwright stores them in a `-snapshots` folder
- Generate and compare baselines in the same environment, ideally the official Playwright Docker image in CI, because fonts, GPU and operating system change the output
- Update baselines with `npx playwright test --update-snapshots` only when a visual change is intended, and say which change in the QA report
- Never update a baseline just to make a failing test pass

## Reviewing a failure

Playwright saves the expected, actual and diff images in the test results folder. Check the diff:
- Small speckles at edges usually mean anti-aliasing noise, so consider the threshold
- Shifted objects mean the camera or layout changed, so check for a bug
- Different colours mean a material, lighting or tone mapping change, so check with the technical artist
