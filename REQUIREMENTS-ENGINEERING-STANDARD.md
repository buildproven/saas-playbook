# Requirements Engineering Standard

**Version:** 1.0
**Created:** 2026-01-01
**Standard:** Based on IEEE 29148:2018

---

## 1. Requirements Hierarchy

This standard follows a strict decomposition hierarchy per [IEEE 29148](https://standards.ieee.org/ieee/29148/6937/):

```
Epic (UC-XX)              → High-level business capability
  └─ Feature (F-XX)       → User-facing functionality
       └─ User Story      → "As a X, I want Y, so that Z"
            └─ Requirement (REQ-X.XX.XX) → Atomic, testable specification
                 └─ Acceptance Criteria → GIVEN/WHEN/THEN conditions
```

### Example Decomposition

```
UC-01: Purchase Products (Epic)
├─ F-01: Starter Kit Purchase (Feature)
│   ├─ US-01.01: "As a founder, I want to buy the Starter Kit, so I can build my SaaS"
│   │   ├─ REQ-F.01.01: Display pricing tiers on /pricing page
│   │   │   └─ AC: GIVEN user visits /pricing WHEN page loads THEN show pricing tier options
│   │   ├─ REQ-F.01.02: Create Stripe checkout session
│   │   │   └─ AC: GIVEN user clicks Buy WHEN tier selected THEN redirect to Stripe
│   │   ├─ REQ-F.01.03: Validate Stripe webhook signature
│   │   │   └─ AC: GIVEN webhook received WHEN signature invalid THEN return 400
│   │   ├─ REQ-F.01.04: Process checkout.session.completed event
│   │   │   └─ AC: GIVEN valid webhook WHEN event=completed THEN create purchase record
│   │   ├─ REQ-F.01.05: Store purchase in database
│   │   │   └─ AC: GIVEN purchase processed WHEN saved THEN include email, tier, timestamp
│   │   └─ REQ-F.01.06: Send confirmation email
│   │       └─ AC: GIVEN purchase saved WHEN email sent THEN include receipt + access link
│   └─ US-01.02: "As an admin, I want purchase notifications..."
│       ├─ REQ-F.01.07: Send admin notification email
│       ├─ REQ-F.01.08: Log purchase to monitoring
│       └─ REQ-F.01.09: Update revenue metrics
├─ F-02: Idea Validation Purchase (Feature)
│   └─ [similar decomposition...]
└─ F-03: Factory Application (Feature)
    └─ [similar decomposition...]
```

---

## 2. Requirements Coverage Targets

### Minimum Requirements by Project Complexity

| Project Size          | Use Cases | Features | Requirements | Tests |
| --------------------- | --------- | -------- | ------------ | ----- |
| Simple (landing page) | 3-5       | 10-15    | 50-100       | 80+   |
| Medium (SaaS MVP)     | 10-15     | 30-50    | 150-300      | 250+  |
| Complex (Platform)    | 20-30     | 60-100   | 400-700      | 600+  |
| Enterprise            | 40+       | 150+     | 1000+        | 1500+ |

**Classification:** Complex Platform → **Target: 500+ requirements**

### Requirements per Domain (IEEE 29148 categories)

| Domain                 | Expected % | Target Count |
| ---------------------- | ---------- | ---------------- |
| Functional (REQ-F)     | 60%        | ~300             |
| Non-Functional (REQ-N) | 25%        | ~125             |
| - Performance          | 5%         | 25               |
| - Security             | 8%         | 40               |
| - Reliability          | 5%         | 25               |
| - Accessibility        | 4%         | 20               |
| - Scalability          | 3%         | 15               |
| Interface (REQ-I)      | 10%        | ~50              |
| Data (REQ-D)           | 5%         | ~25              |
| **Total**              | 100%       | **~500**         |

---

## 3. V-Model Test Mapping

Per [V-Model methodology](<https://en.wikipedia.org/wiki/V-model_(software_development)>):

```
Requirements ────────────────────────────────► System Tests (E2E)
     │                                              │
     ▼                                              │
  Design ────────────────────────────────► Integration Tests
     │                                              │
     ▼                                              │
Implementation ─────────────────────────────► Unit Tests
```

### Test Type by Requirement Level

| Requirement Level         | Test Type   | Coverage Target         |
| ------------------------- | ----------- | ----------------------- |
| Use Case (UC-XX)          | E2E/System  | 100% UCs tested         |
| Feature (F-XX)            | Integration | 100% features tested    |
| Requirement (REQ-X.XX.XX) | Unit        | 100% atomic reqs tested |
| Acceptance Criteria       | All levels  | 100% ACs verified       |

### Test Ratio Guidelines

| Test Type         | Ratio to Requirements   | Target |
| ----------------- | ----------------------- | ---------- |
| Unit Tests        | 2:1 (2 tests per req)   | 1000+      |
| Integration Tests | 1:2 (1 test per 2 reqs) | 250+       |
| E2E Tests         | 1:10 (1 per UC/flow)    | 50+        |
| **Total Tests**   | ~3:1                    | **~1300**  |

---

## 4. V-Cycle Gates for Requirements

### Gate 1: Requirements Completeness (Before Architecture)

```typescript
interface RequirementsGate {
  minimumUseCases: 10 // At least 10 use cases
  minimumFeatures: 30 // At least 30 features
  minimumRequirements: 150 // At least 150 atomic reqs (MVP)
  minimumPaidRequirements: 400 // For paid projects
  acceptanceCriteriaCoverage: 100 // All reqs have AC
  traceabilityComplete: true // All reqs traced to UC
}
```

### Gate 2: Test Coverage (Before Deploy)

```typescript
interface TestCoverageGate {
  mvpCodeCoverage: 50 // 50% line coverage (MVP)
  paidCodeCoverage: 80 // 80% line coverage (paid)
  requirementsCoverage: 100 // 100% reqs have tests
  unitTestRatio: 2 // 2 unit tests per req minimum
  integrationCoverage: 80 // 80% of integration paths
  e2eCriticalPaths: 100 // 100% critical paths tested
}
```

### Gate 3: Traceability Matrix (Continuous)

Every requirement must trace:

- **Backward:** To parent UC/Feature/User Story
- **Forward:** To design decisions, implementation, tests
- **Verification:** To test results/evidence

---

## 5. Requirement ID Schema

```
REQ-{TYPE}.{AREA}.{NUMBER}

TYPE:
  F = Functional
  N = Non-Functional
  I = Interface
  D = Data

AREA (for Functional):
  01 = Purchase/Billing
  02 = Validation Service
  03 = Factory/Build
  04 = CLI Commands
  05 = Admin Dashboard
  06 = V-Cycle Pipeline
  07 = Agent System
  08 = Email/Notifications
  09 = Growth/Launch
  10 = Auth/Users
  11 = Starter Kit
  12 = Quality System

AREA (for Non-Functional):
  01 = Performance
  02 = Reliability
  03 = Security
  04 = Quality/Testing
  05 = Scalability
  06 = Maintainability
  07 = Accessibility
  08 = Usability

Example: REQ-F.04.15 = Functional, CLI Commands, 15th requirement
```

---

## 6. Requirement Quality Criteria (IEEE 29148)

Every requirement MUST be:

| Criterion       | Definition                   | Validation                |
| --------------- | ---------------------------- | ------------------------- |
| **Necessary**   | Required for system success  | Traced to business need   |
| **Unambiguous** | Single interpretation        | Peer review passes        |
| **Complete**    | All info to implement        | No TBDs or placeholders   |
| **Consistent**  | No conflicts with others     | Cross-check matrix        |
| **Verifiable**  | Testable with finite cost    | Has acceptance criteria   |
| **Traceable**   | Links to source & tests      | In traceability matrix    |
| **Feasible**    | Technically achievable       | Architecture approved     |
| **Atomic**      | Cannot be decomposed further | Single testable statement |

---

## 7. Common Requirements Checklist

### Infrastructure Requirements (~100 baseline)

Every SaaS project should include requirements for:

**Authentication (20+ reqs)**

- Email/password login, registration, forgot password
- OAuth providers (Google, GitHub)
- Session management, token refresh
- MFA support, account lockout
- Password strength, email verification

**Authorization (15+ reqs)**

- Role-based access control
- Permission checking
- Resource ownership
- Admin vs user access
- API key management

**Billing/Payments (25+ reqs)**

- Stripe integration (checkout, webhooks)
- Subscription lifecycle (create, update, cancel)
- Invoice generation, receipt emails
- Proration, upgrades, downgrades
- Failed payment handling, dunning

**Email System (15+ reqs)**

- Transactional email delivery
- Template system, personalization
- Bounce/complaint handling
- Unsubscribe management
- Email tracking (opens, clicks)

**Error Handling (10+ reqs)**

- Global error boundary
- API error responses
- Logging and monitoring
- User-friendly error messages
- Recovery mechanisms

**Accessibility (15+ reqs)**

- WCAG 2.1 AA compliance
- Keyboard navigation
- Screen reader support
- Color contrast, focus indicators
- Form labels and ARIA

---

## 8. Implementation in V-Cycle

### Stage 1: Validate

- Extract 10+ use cases from idea
- Generate initial requirements (~50)
- Output: REQUIREMENTS.md v0.1

### Stage 2: Build (Architecture)

- Tech Lead expands requirements
- Architecture Review checks completeness
- **GATE: Minimum 150 requirements (MVP) or 400 (paid)**
- Output: REQUIREMENTS.md v1.0

### Stage 2: Build (Implementation)

- QA Agent generates tests from requirements
- Code Review checks requirement coverage
- **GATE: 100% requirements have tests**
- Output: test-trace-matrix.md

### Stage 3: Ship

- **GATE: Test coverage meets threshold**
- **GATE: All critical requirements pass**
- Deployment proceeds only if gates pass

---

## References

- [IEEE 29148:2018 - Requirements Engineering](https://standards.ieee.org/ieee/29148/6937/)
- [V-Model (Software Development)](<https://en.wikipedia.org/wiki/V-model_(software_development)>)
- [Atomic Requirements - Tyner Blain](https://tynerblain.com/blog/2010/09/14/atomic-requirements/)
- [SEI - Using V Models for Testing](https://www.sei.cmu.edu/blog/using-v-models-for-testing/)
- [ISO/IEC/IEEE 29148 Templates - ReqView](https://www.reqview.com/doc/iso-iec-ieee-29148-templates/)
