---
name: ci-cd-pipeline
description: How to set up the GitHub Actions CI/CD pipeline for a 3D web application built with this plugin, covering job stages, caching, Playwright in CI, artefacts, preview deployments and safe production deployment. Use when creating or fixing the build, test and deploy pipeline.
user-invocable: false
---

# CI/CD pipeline

## Stages

| Stage | Command | Runs on |
|---|---|---|
| Install | `npm ci` | Every push and pull request |
| Lint | `npm run lint` | Every push and pull request |
| Type check | `npm run typecheck` | Every push and pull request |
| Unit tests | `npm run test` | Every push and pull request |
| Build | `npm run build` | Every push and pull request |
| End-to-end tests | `npm run test:e2e` | Every pull request and `main` |
| Preview deploy | Host-specific | Pull requests |
| Production deploy | Host-specific | `main`, after every check passes, with approval |

## Workflow

`.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true

jobs:
  checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-node@v5
        with:
          node-version-file: .nvmrc
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm run test
      - run: npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: dist
          path: dist

  e2e:
    needs: checks
    runs-on: ubuntu-latest
    container:
      image: mcr.microsoft.com/playwright:<version matching @playwright/test>
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-node@v5
        with:
          node-version-file: .nvmrc
          cache: npm
      - run: npm ci
      - run: npm run test:e2e
      - if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report
```

- Check the current major versions of each action before using them, and pin them
- Pin the Playwright Docker image to the same version as `@playwright/test` in `package.json`; visual baselines depend on it
- Keep the Node version in `.nvmrc` so local and CI builds match

## Deployment

- Preview deployments on pull requests, so reviewers can try the 3D scene on their own devices
- Production deployment only from `main`, only after every check passes, and behind a GitHub environment with required reviewers
- Never deploy to production, push to a remote or create cloud resources unless the task prompt says the user approved it

## Secrets

- Store tokens in GitHub Actions secrets or environment secrets, never in the workflow file
- List every secret the pipeline needs in `docs/deployment.md`, with where to get it and no value
- Give deploy tokens the narrowest scope the host allows

## Keep it fast

- Cache npm through `setup-node`
- Cancel superseded runs with `concurrency`
- Run end-to-end projects in parallel with shards if the suite takes more than about 10 minutes
