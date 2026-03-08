# Quality Gates

A practical quality gate system for SaaS projects. Defines what "done" means at each stage: pre-commit, CI, and release.

Extracted from a production monorepo. Used daily across multiple AI-assisted SaaS projects.

---

## Why Quality Gates

The default developer workflow has no gates. Code ships when it "looks right." This works until:

- A bug reaches production that a test would have caught
- Coverage silently drops to 0% as deadlines approach
- Security issues go undetected until a breach

Quality gates make quality **automatic and measurable**. They don't slow you down — they stop you from shipping broken things.

---

## The Three Gate Levels

```
Pre-commit (seconds)    →   CI (minutes)    →   Release (full run)
lint, format               type-check          coverage thresholds
                           lint                security audit
                           unit tests          E2E tests
                                               build verification
```

### Pre-commit (Fast — must be <10s)

Run on every commit via husky + lint-staged:

```json
// .husky/pre-commit
"lint-staged": {
  "**/*.{ts,tsx}": ["eslint --fix", "prettier --write"],
  "**/*.{js,json,md,yml}": ["prettier --write"]
}
```

Gates: lint errors, formatting. No type-checking (too slow for commit).

### Pre-push

Run before pushing to remote:

```json
// .husky/pre-push
"scripts": {
  "validate:pre-push": "tsc --noEmit && eslint . && npm test"
}
```

Gates: type errors, lint, unit tests.

### CI (Every PR)

Required checks that block merge:

```yaml
# .github/workflows/ci.yml
jobs:
  quality:
    steps:
      - run: npm run type-check
      - run: npm run lint
      - run: npm test -- --coverage
      - run: npm audit --audit-level high
```

Gates: types, lint, tests passing, coverage threshold, no critical vulnerabilities.

### Release

Full quality check before production deploy:

```bash
npm run type-check
npm run lint
npm test -- --coverage
npm run test:e2e
npm audit --audit-level moderate
npm run build
```

Gates: everything in CI + E2E tests + successful production build.

---

## Coverage Thresholds

Set in your test config (Vitest/Jest):

```ts
// vitest.config.ts
export default defineConfig({
  test: {
    coverage: {
      thresholds: {
        statements: 80,
        branches: 80,
        functions: 80,
        lines: 80
      },
      // Fail the run if thresholds aren't met
      reportsDirectory: './coverage'
    }
  }
})
```

### Threshold Strategy

| Project Stage | Minimum | Target |
|--------------|---------|--------|
| Prototype / MVP | 50% | 65% |
| Early production | 65% | 80% |
| Stable product | 80% | 90% |
| Critical/paid tier | 80% | 90%+ |

**Start low, raise incrementally.** Setting 80% on a greenfield project from day 1 is fine. Raising an existing project from 30% to 80% overnight will block all shipping — raise by 10% per sprint instead.

---

## .qualityrc.json Config File

Keep quality config in a committed file so it's version-controlled and visible:

```json
{
  "version": "1.0.0",
  "quality": {
    "level": 95,
    "coverage": {
      "minimum": 80,
      "target": 90
    },
    "gates": {
      "ci": {
        "required": ["type-check", "lint", "coverage-80", "security-basic"],
        "optional": ["coverage-90"]
      },
      "local": {
        "preCommit": ["lint", "format"],
        "prePush": ["type-check", "smart-test"]
      }
    },
    "traceability": {
      "requireReqIds": false,
      "minimumCoverage": 80,
      "generateMatrix": false
    }
  },
  "thresholds": {
    "statements": 80,
    "branches": 80,
    "functions": 80,
    "lines": 80
  }
}
```

**Fields:**

- `quality.level` — Target quality percentage (95 = "ship-ready", 98 = "production-perfect")
- `gates.ci.required` — Checks that block merge if they fail
- `gates.ci.optional` — Checks that run but don't block
- `traceability.requireReqIds` — Enable if you're tracking requirements (see Requirements Engineering Standard)

---

## Security Gate

Always run `npm audit` in CI. The question is what level blocks:

| Flag | Blocks on |
|------|-----------|
| `--audit-level critical` | Only critical CVEs (minimum viable) |
| `--audit-level high` | High + critical (recommended) |
| `--audit-level moderate` | Moderate + above (strict) |

```bash
# Recommended CI check
npm audit --audit-level high
```

Address vulnerabilities before they compound. A `high` that isn't fixed becomes `critical` when the next exploit is published.

---

## Quality Check Script

Add a unified quality check command to package.json:

```json
{
  "scripts": {
    "quality:check": "tsc --noEmit && eslint . && npm test",
    "quality:ci": "npm run quality:check && npm run test:coverage && npm audit --audit-level high",
    "quality:release": "npm run quality:ci && npm run test:e2e && npm run build"
  }
}
```

Run `quality:check` locally, `quality:ci` in CI, `quality:release` before deploying.

---

## Automating the Loop

Manual quality checks are forgotten under deadline pressure. Automate at every boundary:

1. **Pre-commit hook** — fastest checks only, runs every commit
2. **Pre-push hook** — type-check + tests, runs before push
3. **CI workflow** — full gate, blocks PR merge
4. **Deploy gate** — release check, blocks production deploy

With all four in place, the question shifts from "did we check quality?" to "did it pass?" — which is objectively answerable.

---

## Common Failure Modes

| Problem | Fix |
|---------|-----|
| Hooks bypass with `--no-verify` | Make it a policy: never use `--no-verify`. Address the root cause. |
| Coverage drops silently | Set `reportsDirectory` so CI fails on threshold breach |
| `npm audit` ignored | Set `--exit-code` flag so it actually fails the CI step |
| E2E tests only run locally | Add E2E to CI, even if slow — flaky E2E beats no E2E |
| Quality gate turns off during crunch | Make removing gates require PR review + comment justification |

---

## How to Use

1. Copy `.qualityrc.json` to your project root
2. Add `quality:check`, `quality:ci`, `quality:release` scripts to `package.json`
3. Configure husky hooks (see Testing Strategy for setup)
4. Set coverage thresholds in your test config matching `.qualityrc.json`
5. Add CI workflow that runs `quality:ci` on every PR

Raise thresholds as the codebase matures.
