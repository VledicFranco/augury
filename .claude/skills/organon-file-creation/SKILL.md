---
name: organon-file-creation
description: Creates new organon files (ETHOS.md, PHILOSOPHY.md, PROTOCOL.md, README.md) with correct structure, frontmatter, scope inheritance, and bidirectional references. Use when adding a new domain, feature, component, or scope that needs organon documentation.
protocol_id: PROTO-ORG-6
protocol_file: organon/protocols/PROTOCOLS.md
tools: [organon-generate, organon-validate, organon-verify]
loads:
  - organon/ETHOS.md
  - CLAUDE.md
methodology_version: "0.5.1"
---

# Organon File Creation Workflow

> Creates valid organon files with correct placement, structure, and references.

## Methodology Context

This workflow implements the **Define** step of the Organon enforcement loop. It creates the organon artifacts (ETHOS.md for constraints, PHILOSOPHY.md for rationale, PROTOCOL.md for procedures) that define behavioral constraints before code implements them. For methodology background, read `organon/PRIMER.md`.

---

## When to Use This Skill

Use this skill when:
- **Adding a new domain** that needs its own ETHOS.md
- **Adding a new feature** organon
- **Creating PHILOSOPHY.md** for design rationale
- **Creating PROTOCOL.md** for procedures
- **Adding a new scope directory** that needs a README.md router

---

## Scope Classification

| Scope | Question | Characteristics |
|-------|----------|-----------------|
| **product** | "What is this project?" | Top-level, inherits from meta-organon only |
| **domain** | "What business concepts exist?" | Bounded context, ≥3 unique concepts |
| **feature** | "What can users do?" | Cross-cutting capability |
| **component** | "Where is the code?" | Code module, dependency relationships |

**Decision heuristic:** Has ≥3 unique concepts + lifecycle → domain. Crosses multiple domains → feature.

---

## Steps

### Step 1: Classify Scope

Determine product, domain, feature, or component.

### Step 2: Check Parent Scope

Load the parent scope's ETHOS.md. New file must inherit, never contradict parent constraints.

### Step 3: Select Template

| Artifact | Required Sections |
|----------|-------------------|
| ETHOS.md | Identity (IS/IS NOT), Invariants, Principles, Decision Heuristics |
| PHILOSOPHY.md | The Problem, The Bet, Design Decisions, Trade-offs |
| PROTOCOL.md | Goal, Preconditions, Steps, Verification, Recovery |
| README.md | Contents table with paths, types, descriptions |

### Step 4: Determine File Placement

Follow Pattern A (dedicated `organon/` directory):

    organon/
    ├── domains/
    │   └── <domain-name>/
    │       ├── ETHOS.md
    │       ├── PHILOSOPHY.md
    │       └── README.md
    └── protocols/
        └── PROTOCOLS.md

### Step 5: Generate Frontmatter

Required fields: type, scope, name, version, summary, token_estimate.

    ---
    type: [constraints|rationale|procedures|navigation]
    scope: [product|domain|feature|component]
    name: [kebab-case-name]
    version: "1.0"
    summary: [One-sentence, max 200 chars]
    token_estimate: [number, ~12 tokens/line]
    inherits_from: [parent-scope-names]
    ---

### Step 6: Write Content

**For ETHOS.md:** ≥3 IS statements, ≥3 IS NOT statements, ≥3 invariants with enforcement, ≥3 prioritized principles, ≥5 heuristics.

**For PHILOSOPHY.md:** Problem with root causes, bet (falsifiable), ≥5 trade-offs, ≥3 alternatives.

**For PROTOCOL.md:** Goal, ≥3 preconditions, numbered steps, verification checklist, recovery table.

**For README.md:** Under 100 lines, contents table with all children.

### Step 7: Validate

    organon validate <path-to-new-file>
    organon verify --gate triplets

### Step 8: Check Bidirectional References

If setting `inherits_from`, ensure parent exists. If protocol has `workflow`, ensure workflow has matching `protocol_id`.

---

## Verification

- [ ] `organon validate` passes
- [ ] File is in correct directory per scope
- [ ] Frontmatter has all required fields
- [ ] Section headings match template for artifact type
- [ ] Parent scope constraints inherited, not contradicted

---

## Error Recovery

| Failure | Recovery Action |
|---------|-----------------|
| Wrong scope classification | Apply heuristic: ≥3 concepts → domain, cross-cutting → feature. |
| `organon validate` fails | Check frontmatter for missing fields, wrong type enum. |
| Parent scope contradiction | Remove contradicting constraint. Child scopes can only add, never relax. |
| Bidirectional reference broken | Add missing back-reference. Run `organon verify --gate triplets`. |
