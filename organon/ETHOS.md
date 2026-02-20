---
type: constraints
scope: product
name: augury
version: "1.0"
summary: Behavioral constraints for Augury — organizational health diagnosis and strategic execution methodology
token_estimate: 2300
invariants_count: 8
principles_count: 5
heuristics_count: 8
inherits_from: []
load_priority: high
audience: [llm, human]
---

# Augury — Ethos

> Behavioral constraints for agents and humans working on Augury. This defines what the methodology IS, what it must NEVER violate, and how to make decisions when tradeoffs arise.

---

## Identity

### What Augury IS

- A **methodology** for diagnosing organizational health, classifying challenges, and computing optimal execution paths through a solution dependency graph
- A **data model** built on four entities — Domains, Areas, Challenges, and Solutions — connected through measurable Health & Performance Metrics (HPMs)
- A **priority engine** that uses effort-normalized impact and dependency structure to answer "what should we work on next?"
- A **convergence framework** that routes multiple improvements through shared pilot projects to maximize learning and impact per effort
- An **open-source toolkit** (MIT) that provides both the methodology specification and applications to operate it

### What Augury IS NOT

- Not a project management tool — no Gantt charts, no sprint boards, no task assignment. Augury answers "what matters most and in what order," not "who does what by when"
- Not prescriptive about domain content — Augury provides the structure (how to measure, classify, prioritize), not the substance (which HPMs matter for your organization)
- Not a dashboard-only system — the methodology defines how to think about organizational health, the app visualizes and computes. Without the methodology, the app is meaningless
- Not a one-time assessment framework — Augury is a continuous cycle: measure → prioritize → execute → re-measure. A static snapshot is not Augury

---

## Invariants

Non-negotiable constraints. If any of these are violated, the methodology produces incorrect results. Every invariant must be testable and enforceable.

1. **HPM normalization.** All Health & Performance Metrics MUST be expressed as ratios in the range [0, 1] where **higher values = healthier**. The priority algorithm, domain aggregation, and cross-area comparability all depend on this invariant. A metric that violates the range or inverts the direction will produce incorrect priority ordering.

2. **HPM measurability.** Every HPM MUST define five fields: metric (what is measured as a 0→1 ratio), source (where raw data comes from), method (how the value is computed), frequency (how often it is collected), and owner (who is responsible for measurement). A proposed HPM that cannot fill all five fields is not ready to be tracked — it is an aspiration, not a metric.

3. **Solution DAG.** Solution dependencies MUST form a directed acyclic graph. Cycles are invalid — if a cycle is detected, decompose one of the solutions to break it. The DAG enables topological ordering, critical path analysis, and downstream unblock counting. A cycle makes all three impossible.

4. **Full traceability.** Every Challenge MUST affect at least one HPM. Every Solution MUST affect at least one HPM, either directly (declared impact) or as an enabler (inherited from dependents). A challenge without HPMs is an observation, not a challenge. A solution without HPM impact — direct or inherited — is untracked work.

5. **Urgency orthogonality.** Urgency (time-sensitivity) and priority (effort-normalized impact) are independent axes. They are NEVER conflated into a single score. Urgent solutions form a subspace processed first; the priority algorithm applies within both the urgent and normal subspaces independently.

6. **Global ID uniqueness.** Challenge IDs (`C{n}`) and Solution IDs (`S{n}`) are globally unique across the entire challenge space. No namespacing by area or domain. This enables unambiguous cross-references in dependencies, action items, and cross-domain analysis.

7. **Priority determinism.** The priority algorithm MUST be deterministic — same inputs produce the same ordering. The specific formula used MUST be stated explicitly in every strategy definition. Implicit or unstated prioritization is not Augury.

8. **Hierarchy integrity.** The data model follows the hierarchy `Domain → Area → Challenge → Solution[]`. Every challenge belongs to exactly one area, every area belongs to exactly one domain. Solutions may address challenges across areas and domains, but the challenge-level classification is strict. An entity that skips a level (a solution without a challenge, an area without a domain) is structurally invalid.

---

## Principles (Prioritized)

When principles conflict, lower-numbered principles win.

1. **Measurement over intuition.** If you can't measure it, you can't prioritize it. HPMs are the arbiter of organizational health, not opinions, not gut feelings, not seniority. When someone says "this feels important," the response is "what HPM does it move?" This principle is foundational — without it, Augury degenerates into a task list with extra steps.

2. **Traceability over convenience.** Every element in the model must connect to the graph: challenges to HPMs, solutions to challenges, dependencies to solutions. Breaking traceability to save time creates unmeasured work that escapes prioritization. It's always worth the overhead of maintaining the links.

3. **Unblocking over finishing.** Prioritize work that enables other work over work that completes in isolation. A solution with modest direct impact but high downstream count (many solutions it unblocks) is more valuable than a leaf solution with equivalent HPM change. This is why the ground-zero priority formula includes `|downstream(S)|`.

4. **Convergence over fragmentation.** When multiple improvements can be routed through a single pilot project, do it. A pilot that validates CI/CD, infra standards, and deployment patterns simultaneously is worth more than three independent initiatives — even if the pilot takes longer. Convergence produces reference models; fragmentation produces islands.

5. **Adaptation over commitment.** Strategies are re-evaluated on cadence. When HPMs move, priorities shift. A plan made with HPMs at 0 is wrong when HPMs reach 0.4 — the ground-zero formula should give way to the standard formula, urgency should be reassessed, and new challenges should be absorbed. Loyalty to a plan is not a virtue.

---

## Decision Heuristics

| Situation | Action |
|-----------|--------|
| Defining a new metric | Fill all 5 HPM fields (metric, source, method, frequency, owner). If you can't, it's not ready — refine or replace. |
| Natural metric isn't 0→1 | Apply normalization transform: decreasing counts → `ideal/actual`, increasing counts → `actual/target`, decreasing rates → `1 - rate`, subjective → `avg/max`. Always: higher = healthier. |
| Choosing between solutions | Run the priority algorithm. Don't override it without documenting why. |
| Most HPMs are near zero | Use the ground-zero formula: `effort / (expected_HPM_change × (1 + \|downstream(S)\|))`. State this explicitly in the strategy. |
| Solution has no direct HPM impact | Classify as enabler. Compute inherited impact: `avg(expected_HPM_change of direct dependents)`. |
| Cycle detected in solution dependencies | Decompose one solution into two sequential steps to break the cycle. Never delete a dependency to fix a cycle. |
| Urgent vs. high-priority conflict | Process the urgent subspace first. Within it, use the same priority algorithm. Don't let urgency override the algorithm's ordering within its subspace. |
| Strategy feels outdated | Re-measure HPMs. If values have shifted significantly, re-run the priority algorithm and re-evaluate the phase plan. Augury strategies are living documents. |
