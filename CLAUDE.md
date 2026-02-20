# Augury — Project Instructions

> Agent guidance for Augury. Loaded automatically by Claude Code.

---

## Identity

### What Augury IS

- An open-source methodology and application for **organizational health diagnosis and strategic execution**
- A system that tracks **Health & Performance Metrics (HPMs)** — normalized 0→1 indicators of how well an organization functions
- A framework that maps **challenges → solutions** as a dependency graph (DAG), then computes **optimal execution order** via a priority algorithm
- A **convergence engine** — maximize impact per effort by routing multiple improvements through shared pilot projects

### What Augury IS NOT

- Not a generic project management tool (no Gantt charts, no sprint boards)
- Not a dashboard-only system — the methodology defines *how to think* about organizational health, not just how to visualize it
- Not prescriptive about *which* metrics matter — Augury provides the structure, you define the HPMs for your context

---

## Core Concepts

| Concept | Definition |
|---------|------------|
| **HPM** | Health & Performance Metric — a normalized 0→1 measure of organizational capability (e.g., CI pipeline coverage, peer review rate, developer satisfaction) |
| **Challenge** | An identified organizational problem, classified by domain and area, affecting one or more HPMs |
| **Solution** | A proposed action that addresses one or more challenges, with estimated effort and expected HPM impact |
| **Solution DAG** | The dependency graph of solutions — some solutions require others to complete first |
| **Priority Algorithm** | `priority = effort / (current_HPM + expected_HPM_change)` — lower is higher priority. Ground-zero variant values foundational work via downstream unblock count |
| **Convergence Strategy** | Routing multiple solutions through a single pilot project to maximize learning and impact per effort |
| **Domain** | A top-level category of organizational concern (e.g., Developer Experience, Communication, Technology) |
| **Area** | A subcategory within a domain (e.g., CI/CD, Code Quality, Security within Technology) |

---

## Organon Methodology

This project uses the [Organon methodology](https://github.com/VledicFranco/organon) for behavioral consistency in human-LLM collaboration. Organon files define constraints (ETHOS.md), design rationale (PHILOSOPHY.md), and procedures (PROTOCOLS.md) that guide development. The methodology is enforced through an automated loop:

    Define → Bind → Execute → Verify → Compound → Evolve

---

## Quick Reference

- Read `organon/PRIMER.md` to understand the methodology (first session)
- Read `organon/ETHOS.md` before working in this codebase
- Run `organon verify` before committing (checks 9 gates: frontmatter, triplets, references, placeholders, freshness, invariant coverage, workflow quality, tier4 tests, version alignment)
- Run `organon health` for aggregate project integrity score (0-100)

---

## When Verify Fails

Each failure includes a diagnostic code. Common fixes:

| Code | Fix |
|------|-----|
| `FRONTMATTER_MISSING` | Add YAML frontmatter (use `organon generate <path>`) |
| `FRONTMATTER_COUNT_MISMATCH` | Update count fields to match actual content |
| `WORKFLOW_MISSING_TOOLS` | Add `tools:` array to skill frontmatter |
| `REFERENCE_BROKEN_LOADS` | Fix file path in workflow `loads:` array |
| `PLACEHOLDER_DETECTED` | Replace template placeholders with real content |

For full diagnostic code reference: read `organon/methodology-reference.md`.

---

## Decision Heuristics

| Situation | Action |
|-----------|--------|
| Starting a work session | Run `organon verify` for baseline check |
| Before committing | Run `organon verify` to ensure nothing is broken |
| Adding a new domain | Use `/domain-feature-design` skill |
| Creating organon files | Use `/organon-file-creation` skill |
| Reviewing organon quality | Use `/quality-review` skill |
| End of work session | Use `/session-compounding` skill |
| Designing HPMs or solutions | Check `organon/PHILOSOPHY.md` for normalization and priority algorithm rationale |
| Adding a new data model entity | Ensure it maps to the Challenge → Solution → HPM triad |

---

## Project Structure

```
augury/
├── CLAUDE.md                     ← This file (agent instructions)
├── README.md                     ← Project overview and concept
├── LICENSE                       ← MIT
├── organon.config.json           ← Organon CLI configuration
├── organon/
│   ├── ETHOS.md                  ← Product constraints (identity, invariants, principles)
│   ├── PHILOSOPHY.md             ← Design rationale (the why)
│   ├── PRIMER.md                 ← Methodology primer
│   ├── methodology-reference.md  ← Detailed methodology reference
│   ├── README.md                 ← Organon navigation
│   ├── protocols/
│   │   └── PROTOCOLS.md          ← Development procedures
│   └── observations/
│       └── README.md             ← Observation tracking
└── .claude/
    └── skills/                   ← Claude Code workflow bindings
```
