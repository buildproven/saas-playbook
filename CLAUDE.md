# SaaS Playbook — Claude Code Instructions

This is a pure documentation repo — a collection of battle-tested engineering standards for SaaS projects, extracted from a production monorepo. There is no application code, no build system, no tests to run.

## File Inventory

| Document | Covers |
|----------|--------|
| TESTING-STRATEGY.md | Risk-based testing framework, test pyramid adapted for AI-generated code, coverage thresholds |
| REQUIREMENTS-ENGINEERING-STANDARD.md | IEEE 29148-based requirement decomposition, traceability matrices, scaling from 50 to 500+ requirements |
| BACKLOG-MAINTENANCE.md | Scoring and prioritization system, value-driven formula, archival policy, quarterly cleanup |
| SOP-output-consistency.md | Standard operating procedure for consistent deliverables across product tiers |
| QUALITY-GATES.md | Pre-commit, CI, and release quality gates, `.qualityrc.json` config, security audits |
| LAUNCH-TEMPLATES.md | Social and email templates for launching developer tools / SaaS products |
| BACKLOG.md | Prioritized backlog of planned additions to this playbook |

## How to Use

- **Starting a new project?** Read TESTING-STRATEGY.md first — it sets the quality bar.
- **Ready to ship?** Apply QUALITY-GATES.md to lock in release criteria.
- **Growing backlog?** Apply BACKLOG-MAINTENANCE.md scoring to prioritize ruthlessly.
- **Scaling the team?** REQUIREMENTS-ENGINEERING-STANDARD.md ensures nothing falls through cracks.
- **Shipping to customers?** SOP-output-consistency.md defines your quality bar per tier.
- **Launching publicly?** LAUNCH-TEMPLATES.md for social and email copy.

## Conventions

- **Markdown only.** All content lives in `.md` files at the repo root.
- **Concise.** Standards should be actionable, not academic. If it doesn't change behavior, cut it.
- **Conventional commits.** `docs:` for content changes, `chore:` for repo maintenance.
- **No code.** This repo contains standards and templates, not implementations.
- **Cross-references.** Link between docs using relative paths (e.g., `[Quality Gates](QUALITY-GATES.md)`).
