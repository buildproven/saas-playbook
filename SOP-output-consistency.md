# SOP: Output Consistency Per Tier

Standard Operating Procedure for ensuring consistent deliverables across products.

## Overview

Each product has defined deliverables that must be delivered consistently. This document specifies what each tier delivers and the quality standards for each output.

---

## Product: Validator ($297)

### Deliverables

| Deliverable          | Format   | Location                                | Quality Standard                    |
| -------------------- | -------- | --------------------------------------- | ----------------------------------- |
| Validation Report    | Markdown | `docs/validations/YYYY-MM-DD-{slug}.md` | 10+ pages                           |
| Verdict              | Text     | In report header                        | GO / PIVOT / NO-GO                  |
| Demand Analysis      | Section  | Report section                          | Real search data from 3+ sources    |
| Competition Analysis | Section  | Report section                          | Top 5 competitors analyzed          |
| Pricing Strategy     | Section  | Report section                          | Specific price point recommendation |
| Executive Summary    | Section  | Report section                          | 1-page summary at top               |

### Delivery Timeline

- **Standard**: 24 hours from purchase
- **Deep Research**: 48 hours (multi-LLM synthesis)

### Quality Checklist

- [ ] Report is 10+ pages
- [ ] Verdict is clearly stated (GO/PIVOT/NO-GO)
- [ ] Demand analysis includes real URLs/sources
- [ ] At least 5 competitors analyzed with pricing
- [ ] Specific price point recommended with justification
- [ ] Executive summary fits on one page
- [ ] No placeholder text or "[TODO]" markers
- [ ] Customer's idea description is accurate in report

### Report Template Structure

```markdown
# Validation Report: {Project Name}

## Executive Summary

- Verdict: **GO** / **PIVOT** / **NO-GO**
- Confidence: X/10
- Key Finding: [One sentence]

## Demand Analysis

- Search volume data
- Reddit/forum discussions found
- Social media signals

## Competition Analysis

| Competitor | Pricing | Strengths | Weaknesses | Gap |
| ---------- | ------- | --------- | ---------- | --- |

## Pricing Strategy

- Recommended price: $X
- Justification: ...

## Recommendations

1. ...
2. ...
3. ...

## Sources

- [URLs used in research]
```

---

## Product: Starter Kit ($199 Single / $349 Unlimited)

### Deliverables

| Deliverable        | Format     | Location              | Quality Standard       |
| ------------------ | ---------- | --------------------- | ---------------------- |
| GitHub Repo Access | Invite     | Email                 | Private repo           |
| README             | Markdown   | `/README.md`          | Setup instructions     |
| Source Code        | TypeScript | `/src/*`              | Production-ready       |
| Authentication     | Code       | NextAuth configured   | Email + OAuth          |
| Payments           | Code       | Stripe configured     | Subscriptions + portal |
| Database           | Schema     | Prisma schema         | Type-safe              |
| Tests              | Code       | `/tests/*`            | 80% coverage target    |
| Deploy Config      | YAML/JSON  | Vercel config         | One-click deploy       |
| Design Tokens      | JSON       | `/design-tokens.json` | Personality applied    |

### Delivery Timeline

- **Standard**: 24 hours from purchase
- **GitHub invite sent**: Within 24 hours

### Quality Checklist

- [ ] `pnpm install` runs without errors
- [ ] `pnpm build` completes successfully
- [ ] `pnpm test` passes all tests
- [ ] `pnpm dev` starts development server
- [ ] Authentication flow works (sign up → sign in → sign out)
- [ ] Stripe test mode checkout works
- [ ] Database migrations run successfully
- [ ] Vercel deploy button works
- [ ] README has clear setup instructions
- [ ] All environment variables documented
- [ ] No hardcoded secrets in code
- [ ] Design tokens applied (if personality specified)

### Tech Stack (Fixed)

| Layer     | Technology          | Version |
| --------- | ------------------- | ------- |
| Framework | Next.js             | 15.x    |
| Language  | TypeScript          | 5.x     |
| Styling   | Tailwind CSS        | 3.x     |
| Database  | PostgreSQL + Prisma | Latest  |
| Auth      | NextAuth            | 5.x     |
| Payments  | Stripe              | Latest  |
| Testing   | Vitest              | Latest  |
| Hosting   | Vercel              | N/A     |

---

## Delivery Process

### 1. Purchase Received

```
Stripe webhook → Create customer record → Queue delivery
```

### 2. Validation (Validator product)

```
Run Revenue Hunter V3 → Generate report → Email customer
```

### 3. Scaffold (Starter Kit product)

```
Create private repo → Run scaffold → Apply design tokens → Send invite
```

### 4. Confirmation Email

Both products send:

- Confirmation of purchase
- Delivery timeline
- Support contact
- Link to deliverable

---

## Quality Gates

### Before Delivery

1. **Validator**: Report passes AI review (no hallucinations, real sources)
2. **Starter Kit**: Build passes, tests pass, lint passes

### After Delivery

1. **Customer satisfaction check**: 7-day follow-up email
2. **Issue tracking**: GitHub issues monitored within 24 hours

---

## Support SLA

| Channel       | Response Time | Hours         |
| ------------- | ------------- | ------------- |
| GitHub Issues | 24 hours      | Business days |
| Email         | 48 hours      | Business days |

---

## Refund Policy

| Product     | Window  | Process                   |
| ----------- | ------- | ------------------------- |
| Validator   | 7 days  | Full refund, no questions |
| Starter Kit | 30 days | Full refund, no questions |

Refunds processed via Stripe within 5-10 business days.

---

## Version History

| Version | Date       | Changes                     |
| ------- | ---------- | --------------------------- |
| 1.0     | 2025-12-28 | Initial SOP created (P-009) |

---

_Last updated: 2025-12-28_
