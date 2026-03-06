# Testing Strategy

Comprehensive testing guide for contributors and maintainers.

## Quick Reference

```bash
# Run all tests
pnpm test

# Watch mode (development)
pnpm test:watch

# Coverage report
pnpm test:coverage

# Run tests for specific package
pnpm test --filter agents
pnpm test --filter shared
```

## Test Architecture

### Directory Structure

```
your-project/
├── vitest.config.ts          # Root config (unified test running)
├── tests/                    # Integration & E2E tests
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── packages/
│   ├── agents/
│   │   ├── vitest.config.ts  # Package-specific overrides
│   │   └── src/**/*.test.ts  # Co-located unit tests
│   ├── shared/
│   │   └── src/**/*.test.ts
│   └── starter-kit/
│       └── tests/            # Scaffold tests
└── apps/
    ├── factory/
    │   └── src/**/*.test.ts  # CLI tests
    └── marketing/
        └── src/**/*.test.ts  # Component tests
```

### Test Types

| Type            | Location             | Purpose                      | Speed  |
| --------------- | -------------------- | ---------------------------- | ------ |
| **Unit**        | `src/**/*.test.ts`   | Individual functions/modules | Fast   |
| **Integration** | `tests/integration/` | Module interactions          | Medium |
| **E2E**         | `tests/e2e/`         | Full user flows (Playwright) | Slow   |

## Coverage Thresholds

Defined in root `vitest.config.ts`:

```typescript
thresholds: {
  statements: 50,
  branches: 50,
  functions: 50,
  lines: 50,
}
```

**By project maturity** (`.qualityrc.json`):

- **Paid projects**: 80% minimum
- **Free projects**: 50% minimum

## Writing Tests

### Unit Test Pattern

```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { functionUnderTest } from './module'

describe('functionUnderTest', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('should handle valid input', () => {
    const result = functionUnderTest({ valid: true })
    expect(result).toBeDefined()
  })

  it('should throw on invalid input', () => {
    expect(() => functionUnderTest(null)).toThrow()
  })
})
```

### Mocking External APIs

```typescript
import { vi } from 'vitest'

// Mock fetch for API tests
vi.stubGlobal('fetch', vi.fn())

beforeEach(() => {
  vi.mocked(fetch).mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({ data: 'test' }),
  } as Response)
})
```

### Agent Test Pattern

```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest'

// Mock the SDK while preserving exports
vi.mock('../sdk', async importOriginal => {
  const actual = await importOriginal<typeof import('../sdk')>()
  return {
    ...actual,
    runAgent: vi.fn().mockResolvedValue({ success: true }),
  }
})

describe('AgentRunner', () => {
  it('should execute agent successfully', async () => {
    const result = await runAgentV2({ input: 'test' })
    expect(result.success).toBe(true)
  })
})
```

### Stripe Webhook Tests

```typescript
import { createMockStripeEvent } from '../test-utils/stripe'

it('should handle checkout.session.completed', async () => {
  const event = createMockStripeEvent('checkout.session.completed', {
    customer_email: 'test@example.com',
  })

  const response = await handleWebhook(event)
  expect(response.status).toBe(200)
})
```

## Test Data & Fixtures

### Location

```
packages/agents/src/__fixtures__/
packages/shared/src/__fixtures__/
tests/fixtures/
```

### Creating Fixtures

```typescript
// __fixtures__/validation-report.ts
export const mockValidationReport = {
  idea: 'Test SaaS',
  score: 85,
  verdict: 'GO',
  marketSize: '$1B',
}
```

## Running Tests

### Local Development

```bash
# All tests (recommended before commit)
pnpm test

# Specific package
pnpm test --filter agents

# Watch mode for TDD
pnpm test:watch

# Single file
pnpm vitest run packages/agents/src/runners/tech-lead-v2.test.ts
```

### CI Pipeline

Tests run automatically on:

- Every PR (GitHub Actions)
- Push to main/develop

**CI workflow** (`.github/workflows/quality.yml`):

1. Install dependencies
2. Type check (`pnpm type-check`)
3. Lint (`pnpm lint`)
4. Test with coverage (`pnpm test:coverage`)
5. Upload coverage report

### Pre-commit Hook

Tests are NOT run in pre-commit (too slow). Instead:

- Lint-staged runs ESLint/Prettier
- Coverage warning script checks new files

## Debugging Tests

### VSCode Integration

Add to `.vscode/launch.json`:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Debug Vitest",
  "program": "${workspaceRoot}/node_modules/vitest/vitest.mjs",
  "args": ["run", "${relativeFile}"],
  "cwd": "${workspaceRoot}",
  "console": "integratedTerminal"
}
```

### Common Issues

**1. Mock not working**

```typescript
// Wrong: mock after import
import { fn } from './module'
vi.mock('./module')

// Right: mock before import (hoisted automatically by vitest)
vi.mock('./module')
import { fn } from './module'
```

**2. Async test timeout**

```typescript
it('should complete async operation', async () => {
  // Increase timeout for slow operations
}, 10000)
```

**3. Environment variables**

```typescript
beforeEach(() => {
  vi.stubEnv('API_KEY', 'test-key')
})

afterEach(() => {
  vi.unstubAllEnvs()
})
```

## Test Matrix

| Package              | Tests | Coverage | Status |
| -------------------- | ----- | -------- | ------ |
| agents          | 800+  | 75%      | ✅     |
| shared          | 150+  | 55%      | ✅     |
| apps/factory         | 100+  | 50%      | ✅     |
| apps/marketing       | 50+   | 32%      | 🔄     |
| packages/starter-kit | 30+   | 34%      | 🔄     |

## Best Practices

1. **Co-locate tests**: Put `*.test.ts` next to source files
2. **Descriptive names**: `it('should return validation error when email is invalid')`
3. **One assertion per test** (when possible)
4. **Mock external dependencies**: APIs, databases, file system
5. **Test edge cases**: null, undefined, empty arrays, boundary values
6. **No secrets in tests**: Use fixtures and mock data
7. **Clean up**: Reset mocks and state in `beforeEach`/`afterEach`

## Requirements Traceability

Each test should trace to a requirement when applicable:

```typescript
/**
 * @requirement REQ-AUTH-001: User authentication
 */
describe('AuthService', () => {
  // Tests...
})
```

See `docs/test-trace-matrix.md` for full traceability matrix.

## Related Documentation

- [REQUIREMENTS.md](./REQUIREMENTS.md) - Feature requirements
- [WORKFLOW.md](./WORKFLOW.md) - Development workflow
- [test-trace-matrix.md](./test-trace-matrix.md) - Test traceability
