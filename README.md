# SaaS Engineering Playbook

Battle-tested engineering standards for SaaS projects. Extracted from a production monorepo.

These aren't theoretical — they were used daily to ship software with AI-assisted development.

## Documents

### [Testing Strategy](TESTING-STRATEGY.md)
Risk-based testing framework. Defines test types, coverage thresholds, and when to use each level. Includes the test pyramid adapted for AI-generated code where integration tests matter more than unit tests.

### [Requirements Engineering Standard](REQUIREMENTS-ENGINEERING-STANDARD.md)
IEEE 29148-based requirement decomposition for SaaS projects. Covers requirement IDs, traceability matrices, and how to scale from prototype to complex platform (50 to 500+ requirements).

### [Backlog Maintenance](BACKLOG-MAINTENANCE.md)
Scoring and prioritization system for backlogs. Value-driven formula: `(Revenue + Retention + Differentiation) / Effort`. Includes archival policy and quarterly cleanup process.

### [Output Consistency SOP](SOP-output-consistency.md)
Standard operating procedure for consistent deliverables across product tiers. Defines what each tier delivers and the quality bar for each output.

## How to Use

1. **Starting a new project?** Read Testing Strategy first — it sets the quality bar.
2. **Growing backlog?** Apply the Backlog Maintenance scoring to prioritize ruthlessly.
3. **Scaling the team?** Requirements Engineering Standard ensures nothing falls through cracks.
4. **Shipping to customers?** Output Consistency SOP defines your quality bar per tier.

## License

MIT — use, adapt, and distribute freely.
