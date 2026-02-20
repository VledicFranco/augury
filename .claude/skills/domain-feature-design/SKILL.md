---
name: domain-feature-design
description: Guides agents through proper domain/feature design following Organon methodology. Ensures RFCs contain both organon mutation plan (ETHOS.md, PHILOSOPHY.md content) AND technical implementation plan (architecture, API, phases). Use when designing new domains, features, or significant capabilities that require organon evolution.
protocol_id: PROTO-ORG-1
protocol_file: organon/protocols/PROTOCOLS.md
tools: [organon-validate, organon-verify]
loads:
  - organon/ETHOS.md
  - CLAUDE.md
methodology_version: "0.5.1"
---

# Domain/Feature Design Skill

> Design new domains and features using RFC-Driven Evolution — ensures organon defines "should be" before code implements "what is."

## Methodology Context

This workflow implements the **Define** and **Evolve** steps of the Organon enforcement loop. It uses RFC-Driven Evolution — the formal mechanism where RFCs declare both organon mutations (constraint changes) and technical implementation plans. For methodology background, read `organon/PRIMER.md`.

---

## When to Use This Skill

Use this skill when:
- **Adding a new domain** to a product (bounded context with its own organon)
- **Adding a new feature** (cross-cutting capability)
- **Proposing significant changes** that require organon evolution

---

## Core Principle

    ORGANON ("should be") <--cites--> RFC ("will be") --implements--> CODE ("what is")

**RFCs have dual nature:**
1. **Organon mutation plan** — What constraints the domain will define
2. **Technical implementation plan** — How code will implement those constraints

**BOTH are required.** Writing only one is incomplete.

---

## Scope Classification

| Type | Question Answered | Use When |
|------|-------------------|----------|
| **Domain** | "What business concepts exist?" | Rich bounded context, ≥3 unique concepts |
| **Feature** | "What can users do?" | Cross-cutting capability |
| **Component** | "Where is the code?" | Code modules |

**Decision heuristic:** Has ≥3 unique concepts + lifecycle → Domain. Crosses multiple domains → Feature. Code terms → Component.

---

## Steps

### Step 1: Load Context

Read the loaded files:
- `organon/ETHOS.md` — product constraints this domain inherits
- `CLAUDE.md` — project-level guidance

### Step 2: Classify Scope

Determine if this is a domain, feature, or component.

### Step 3: Create RFC

1. Determine next RFC number from `rfcs/` directory
2. Create `rfcs/NNN-<feature-name>.md`

### Step 4: Write Organon Mutation Plan

Write the exact ETHOS.md content (identity, invariants, principles, heuristics) and PHILOSOPHY.md content (problem, bet, trade-offs) that will be created.

**ETHOS.md requirements:**
- ≥3 identity IS statements (specific, not generic)
- ≥3 identity IS NOT statements (real boundaries)
- ≥3 invariants with enforcement mechanisms
- ≥3 prioritized principles (lower number wins)
- ≥5 decision heuristics (situation → action)

**PHILOSOPHY.md requirements:**
- Problem statement with root causes
- The bet (falsifiable hypothesis)
- ≥5 trade-offs with rationale
- ≥3 alternatives considered
- Measurable success criteria

### Step 5: Write Technical Implementation Plan

- Architecture (package structure, core abstractions)
- API design (function signatures, interfaces)
- Phased implementation plan
- ≥5 design decisions linked to domain principles

### Step 6: Self-Review

Verify both plans are present and detailed using the checklist:
- [ ] RFC has both organon mutation plan AND technical implementation plan
- [ ] Invariants are testable (not vague aspirations)
- [ ] Principles are genuinely prioritized
- [ ] Technical plan has architecture, API, and phases

---

## Error Recovery

| Failure | Recovery Action |
|---------|-----------------|
| Can't classify scope | Ask: does it have ≥3 unique concepts + lifecycle? If yes → domain. Crosses domains? → feature. |
| Invariants too vague | Apply "testable?" filter — if you can't write a verification gate, rewrite to be more specific. |
| Missing organon plan | Stop and write exact ETHOS.md/PHILOSOPHY.md content before technical plan. |
| Missing technical plan | Stop and add architecture, API, implementation phases. Both plans required. |
| RFC too large | Split into multiple RFCs with explicit dependency chain. |
