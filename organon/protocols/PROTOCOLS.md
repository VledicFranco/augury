---
type: procedures
scope: product
name: protocols
version: "1.0"
summary: Augury methodology protocols (assessment, HPM definition, priority computation, convergence) and Organon development protocols
token_estimate: 4500
protocols_count: 11
protocols:
  - id: PROTO-AUG-1
    name: Organizational Assessment
    steps: 8
    automation_tier: semi-automated
    workflow: null
    tools: []
    complexity: high
  - id: PROTO-AUG-2
    name: HPM Definition
    steps: 6
    automation_tier: manual
    workflow: null
    tools: []
    complexity: medium
  - id: PROTO-AUG-3
    name: Challenge-Solution Mapping
    steps: 7
    automation_tier: semi-automated
    workflow: null
    tools: []
    complexity: high
  - id: PROTO-AUG-4
    name: Priority Computation
    steps: 6
    automation_tier: manual
    workflow: null
    tools: []
    complexity: medium
  - id: PROTO-AUG-5
    name: Convergence Analysis
    steps: 5
    automation_tier: manual
    workflow: null
    tools: []
    complexity: high
  - id: PROTO-AUG-6
    name: Strategy Re-evaluation
    steps: 6
    automation_tier: semi-automated
    workflow: null
    tools: []
    complexity: medium
  - id: PROTO-ORG-1
    name: RFC-Driven Design
    steps: 6
    automation_tier: automated
    workflow: domain-feature-design
    tools: [organon-validate, organon-verify]
    complexity: high
  - id: PROTO-ORG-4
    name: Verification and Health
    steps: 5
    automation_tier: automated
    workflow: verify-and-health
    tools: [organon-verify, organon-health]
    complexity: low
  - id: PROTO-ORG-5
    name: Session Compounding
    steps: 7
    automation_tier: automated
    workflow: session-compounding
    tools: [organon-verify, organon-health, organon-find]
    complexity: medium
  - id: PROTO-ORG-6
    name: Organon File Creation
    steps: 8
    automation_tier: automated
    workflow: organon-file-creation
    tools: [organon-generate, organon-validate, organon-verify]
    complexity: medium
  - id: PROTO-ORG-7
    name: Quality Review
    steps: 7
    automation_tier: automated
    workflow: quality-review
    tools: [organon-verify, organon-validate, organon-health]
    complexity: high
audience: [llm, human, tooling]
---

# Augury Protocols

> Step-by-step procedures for both the Augury methodology (how to assess and prioritize) and Augury development (how to build the project).

---

## Protocol Overview

### Methodology Protocols

| ID | Name | Automation | Complexity |
|----|------|------------|------------|
| PROTO-AUG-1 | Organizational Assessment | Semi-automated | High |
| PROTO-AUG-2 | HPM Definition | Manual | Medium |
| PROTO-AUG-3 | Challenge-Solution Mapping | Semi-automated | High |
| PROTO-AUG-4 | Priority Computation | Manual | Medium |
| PROTO-AUG-5 | Convergence Analysis | Manual | High |
| PROTO-AUG-6 | Strategy Re-evaluation | Semi-automated | Medium |

### Development Protocols

| ID | Name | Workflow | Complexity |
|----|------|----------|------------|
| PROTO-ORG-1 | RFC-Driven Design | domain-feature-design | High |
| PROTO-ORG-4 | Verification and Health | verify-and-health | Low |
| PROTO-ORG-5 | Session Compounding | session-compounding | Medium |
| PROTO-ORG-6 | Organon File Creation | organon-file-creation | Medium |
| PROTO-ORG-7 | Quality Review | quality-review | High |

---

# Methodology Protocols

---

## PROTO-AUG-1: Organizational Assessment

> The full assessment cycle — from raw observations to a prioritized strategy.

### Goal

Produce a complete challenge space with HPMs, challenges, solutions, dependency graph, and priority-ordered execution plan.

### Preconditions

- Access to the organization (interviews, codebase, tooling, processes)
- A defined scope — the whole org, a department, or a specific concern area

### Steps

1. **Survey the landscape.** Conduct interviews, review codebases, audit tooling and processes. Gather raw observations without classifying them yet. Record everything — even things that seem minor. The goal is signal collection, not signal processing.

2. **Identify domains.** Group observations into top-level categories of organizational concern. Typical domains: Developer Experience, Organizational Structure, Communication, Technology, Definitions. Domains should be non-overlapping and collectively cover all observations.

3. **Define areas within domains.** Decompose each domain into specific concern areas. Each area must have at least one measurable HPM — if you can't define an HPM for an area, it's an observation, not an area. Apply PROTO-AUG-2 (HPM Definition) for each area.

4. **Classify challenges.** Convert observations into formal challenges. Each challenge belongs to exactly one area (INV-8: hierarchy integrity) and affects one or more HPMs (INV-4: full traceability). Assign globally unique IDs (`C1`, `C2`, ...).

5. **Propose solutions.** For each challenge, propose one or more solutions. Apply PROTO-AUG-3 (Challenge-Solution Mapping) to define effort, HPM impact, and dependencies. Assign globally unique IDs (`S1`, `S2`, ...).

6. **Build the dependency graph.** Connect solutions via `depends-on` relationships. Validate the graph is a DAG — no cycles (INV-3). Compute downstream counts for each solution.

7. **Compute priorities.** Apply PROTO-AUG-4 (Priority Computation). Choose the appropriate formula variant (standard vs ground-zero). Identify the urgent subspace.

8. **Define the strategy.** Select a subset of solutions for execution. Optionally divide into phases (phases must respect the DAG). Set time horizon, measurement cadence, and review cadence. Apply PROTO-AUG-5 (Convergence Analysis) to identify pilot project opportunities.

### Verification

- [ ] Every area has at least one HPM with all 5 fields defined
- [ ] Every challenge has at least one HPM affected
- [ ] Every solution has HPM impact (direct or inherited as enabler)
- [ ] Dependency graph is a valid DAG (no cycles)
- [ ] Priority formula is explicitly stated
- [ ] Strategy defines time horizon, measurement cadence, and review cadence

---

## PROTO-AUG-2: HPM Definition

> Define a new Health & Performance Metric that satisfies the measurability invariant.

### Goal

Produce an HPM with all 5 required fields, normalized to [0, 1], where higher = healthier.

### Preconditions

- An area that needs measurement
- Knowledge of available data sources

### Steps

1. **Name the metric.** Choose a name that describes the health signal, not the raw data. "CI pipeline coverage" not "count of repos with CI." The name should make the healthy direction obvious.

2. **Define the ratio.** Express the metric as a fraction where the result is in [0, 1]. Apply the appropriate normalization transform:
   - Counts that should decrease: `ideal / actual` (capped at 1.0)
   - Counts that should increase: `actual / target` (capped at 1.0)
   - Rates that should decrease: `1 - rate`
   - Subjective measures: `avg_score / max_score`

   Verify: higher values MUST mean healthier (INV-1).

3. **Identify the source.** Where does the raw data come from? GitHub API, CI platform, survey tool, manual audit, project management tool. The source must exist and be accessible — aspirational sources fail the measurability invariant.

4. **Define the method.** Step-by-step computation from source to ratio. Specific enough that someone else (or a script) could reproduce the measurement. "Count repos with passing pipeline config, divide by total repos" — not "assess CI coverage."

5. **Set the frequency.** How often will this metric be collected? Weekly, monthly, quarterly. Higher-frequency metrics enable faster feedback loops but cost more to maintain. Match frequency to the rate of change — a quarterly survey measured weekly is waste; a CI metric measured quarterly misses trends.

6. **Assign an owner.** Who is responsible for collecting this measurement on cadence? A team, a role, or a named individual. Unowned metrics don't get measured.

### Verification

- [ ] All 5 fields defined: metric (ratio), source, method, frequency, owner
- [ ] Ratio produces values in [0, 1]
- [ ] Higher values = healthier
- [ ] Source is accessible (not aspirational)
- [ ] Method is reproducible by someone other than the author

---

## PROTO-AUG-3: Challenge-Solution Mapping

> Map challenges to solutions, define effort and impact, and build the dependency graph.

### Goal

Produce a complete solution set for a challenge space with effort estimates, HPM impact declarations, and dependency links.

### Preconditions

- Challenges classified with HPM assignments (from PROTO-AUG-1 steps 3-4)
- HPMs defined with baselines and targets (from PROTO-AUG-2)

### Steps

1. **Enumerate solutions per challenge.** For each challenge, brainstorm actionable solutions. A solution must be concrete enough to estimate effort and predict HPM impact. "Improve code quality" is not a solution; "enforce minimum test coverage in CI" is.

2. **Estimate effort.** Use the [0, 5) scale:
   - 0 = trivial (hours)
   - 1 = days
   - 2 = weeks
   - 3 = months
   - 4 = quarters

   Estimate conservatively — underestimating effort inflates priority scores and distorts ordering.

3. **Declare HPM impact.** For each solution, state the expected change to each affected HPM. Impacts can span multiple areas and domains — solutions are not confined to their own challenge's HPMs (cross-domain impact). For enabler solutions (zero direct impact), leave impact blank — it will be computed by inheritance in PROTO-AUG-4.

4. **Map dependencies.** For each solution, identify which other solutions must complete first. Dependencies can cross challenges and domains. Express as `S{n} depends-on S{m}`.

5. **Validate the DAG.** Check for cycles. If a cycle exists, decompose one solution into two sequential steps to break it. Never delete a real dependency to resolve a cycle — that hides structural truth.

6. **Identify enablers.** Solutions with zero direct HPM impact but that are depended on by others are enablers. Mark them. Their HPM impact will be computed as the average of their direct dependents' impact during priority computation.

7. **Flag urgency.** Mark time-sensitive solutions as urgent — security vulnerabilities, compliance deadlines, active incidents, contractual obligations. Urgency should be rare. If more than ~15% of solutions are urgent, the flag has lost discriminating power — tighten the criteria.

### Verification

- [ ] Every solution has effort in [0, 5)
- [ ] Every non-enabler solution has at least one HPM impact declared
- [ ] Every enabler has at least one solution that depends on it
- [ ] Dependency graph is a valid DAG
- [ ] Urgent solutions have documented time-sensitivity justification

---

## PROTO-AUG-4: Priority Computation

> Run the priority algorithm to produce an execution ordering.

### Goal

Produce a priority-ordered list of solutions, separated into urgent and normal subspaces.

### Preconditions

- Solutions with effort, HPM impact, and dependencies defined (from PROTO-AUG-3)
- HPM baselines measured (at least estimates)
- Formula variant chosen

### Steps

1. **Choose the formula.** Assess the HPM landscape:
   - If most HPMs have meaningful spread (some near 0, some near 0.5+): use the **standard formula** — `priority = effort / (current_HPM + expected_HPM_change)`
   - If most HPMs are at or near 0 (greenfield/neglected): use the **ground-zero formula** — `priority = effort / (expected_HPM_change × (1 + |downstream(S)|))`

   State the choice explicitly in the strategy document (INV-7: priority determinism).

2. **Compute downstream counts.** For each solution, compute `|downstream(S)|` — the count of solutions transitively unblocked. This is a topological traversal of the DAG. Solutions with no dependents have downstream count 0.

3. **Compute enabler inheritance.** For each enabler solution, compute `expected_HPM_change = avg(expected_HPM_change of direct dependents)`. If a dependent is also an enabler, resolve recursively (leaf-to-root).

4. **Separate subspaces.** Partition solutions into urgent and normal. Apply the same priority formula to both subspaces independently.

5. **Compute priority scores.** For each solution in each subspace, compute the priority score using the chosen formula. Lower score = higher priority. Rank within each subspace.

6. **Validate ordering.** Review the top 10 results in each subspace. Does the ordering match structural intuition? If a solution feels obviously misranked, check the inputs (effort, HPM impact, downstream count) — the issue is almost always in the inputs, not the formula. Document any manual overrides with justification.

### Verification

- [ ] Formula explicitly stated
- [ ] Enabler inheritance computed (no division-by-zero)
- [ ] Urgent and normal subspaces separated
- [ ] Ordering is deterministic — re-running with same inputs produces same result
- [ ] Any manual overrides documented with justification

---

## PROTO-AUG-5: Convergence Analysis

> Identify opportunities to route multiple solutions through shared pilot projects.

### Goal

Find one or more pilot projects where solving multiple challenges simultaneously produces a reusable reference model.

### Preconditions

- Priority-ordered solution list (from PROTO-AUG-4)
- Dependency graph with downstream counts

### Steps

1. **Identify shared dependency clusters.** Find solutions that share upstream dependencies — groups of solutions that all need the same foundation in place. These are natural convergence candidates: if you can validate the foundation AND deliver business value in one project, you avoid redundant foundation-building.

2. **Find business-value anchors.** Convergence requires a real project — not a proof-of-concept, not a sandbox. Look for solutions that deliver direct business value (an app, a tool, a process improvement) and also depend on foundational work. The business-value anchor justifies the investment; the foundational work creates the reference model.

3. **Evaluate convergence benefit.** For each candidate pilot, estimate:
   - How many foundational patterns it validates (CI/CD, deployment, infra contract, testing, etc.)
   - How replicable the patterns are — will the pilot produce templates/guides that other teams can follow?
   - What the coupling risk is — if the pilot fails, how many solutions are blocked?

   Convergence is worth pursuing when the pilot validates 3+ patterns and produces replicable artifacts.

4. **Design the convergence plan.** Define which solutions converge on the pilot, in what order (respecting the DAG), and what the pilot's "reference model package" will contain (repo structure, pipeline config, contract templates, deployment scripts).

5. **Set the rollout trigger.** Define what "pilot validated" means — specific HPM thresholds, successful deployments, pattern documentation complete. Once triggered, the reference model enables cheap rollout (S42 → S9 in the T1 example).

### Verification

- [ ] Pilot delivers direct business value (not just a proof-of-concept)
- [ ] Pilot validates 3+ foundational patterns
- [ ] Coupling risk assessed — fallback plan if pilot fails
- [ ] Reference model package defined (what artifacts the pilot produces)
- [ ] Rollout trigger defined with measurable criteria

---

## PROTO-AUG-6: Strategy Re-evaluation

> Re-measure HPMs, update the challenge space, and re-prioritize.

### Goal

Produce an updated priority ordering that reflects current organizational health, incorporating new challenges and retiring completed solutions.

### Preconditions

- An active strategy with defined measurement cadence
- Measurement cadence reached (e.g., monthly)

### Steps

1. **Re-measure HPMs.** Collect fresh values for all HPMs using their defined methods and sources. Record the new values alongside the previous ones to show trajectory. Flag any HPMs that regressed.

2. **Update solution states.** Mark completed solutions. Check if their HPM impact materialized — if a solution was completed but the HPM didn't improve, investigate. Mark in-progress solutions. Identify blocked solutions whose dependencies haven't resolved.

3. **Absorb new challenges.** During execution, new problems surface. Classify them using PROTO-AUG-1 steps 3-5 (define area, HPMs, challenges, solutions). Add to the existing challenge space with new globally unique IDs.

4. **Check for resolved challenges.** If all HPMs in an area have reached target levels, mark the challenge as resolved. But watch for regression in the next measurement cycle — resolved challenges can reopen.

5. **Re-run priority computation.** Apply PROTO-AUG-4 with updated HPM values, new solutions, and updated downstream counts. Check if the formula variant still applies — if HPMs have moved significantly off zero, the ground-zero formula may no longer be appropriate; switch to the standard formula.

6. **Update the strategy.** Adjust phases if needed. Remove completed solutions. Re-evaluate urgency flags — some urgent items may have been resolved; new urgent items may have emerged. Communicate changes to stakeholders.

### Verification

- [ ] All HPMs re-measured with fresh values
- [ ] Completed solutions marked, HPM impact verified
- [ ] New challenges absorbed with proper IDs and HPM links
- [ ] Priority algorithm re-run with updated inputs
- [ ] Formula variant still appropriate for current HPM landscape
- [ ] Strategy document updated with changes

---

# Development Protocols

---

## PROTO-ORG-1: RFC-Driven Design

> Design new domains and features using RFC-Driven Evolution.

### Goal

Produce a complete RFC with both organon mutation plan and technical implementation plan.

### Steps

1. **Load context.** Read organon/ETHOS.md and CLAUDE.md.
2. **Classify scope.** Domain (bounded context), feature (cross-cutting), or component.
3. **Create RFC.** Determine next number, create rfcs/NNN-name.md.
4. **Write organon plan.** Exact ETHOS.md and PHILOSOPHY.md content.
5. **Write technical plan.** Architecture, API, implementation phases.
6. **Self-review.** Both plans present, invariants testable, principles prioritized.

### Verification

- [ ] RFC has both organon and technical plans
- [ ] Invariants are testable
- [ ] `organon validate` passes on RFC

---

## PROTO-ORG-4: Verification and Health

> Run all verification gates and health checks.

### Goal

Confirm project integrity with actionable fix guidance.

### Steps

1. **Run verification.** `organon verify` (all gates).
2. **Run health.** `organon health` for overall score.
3. **Interpret failures.** Map each to specific fix.
4. **Apply fixes.** Edit specific files and fields.
5. **Re-verify.** Confirm all gates pass.

### Verification

- [ ] All gates pass
- [ ] Health score reported

---

## PROTO-ORG-5: Session Compounding

> Review session output, detect patterns, execute improvements.

### Goal

Convert session learnings into durable improvements.

### Steps

1. **Review session.** Examine git diff of changes.
2. **Detect patterns.** Look for repeated steps, unclear workflows, new heuristics.
3. **Classify.** Tool candidate, protocol update, heuristic addition, or workflow refinement.
4. **Prioritize.** Frequency x impact.
5. **Execute.** Implement highest-priority improvement.
6. **Verify.** `organon verify` and `organon health`.
7. **Record observations.** If session revealed lasting insights.

### Verification

- [ ] At least one improvement identified
- [ ] `organon verify` passes

---

## PROTO-ORG-6: Organon File Creation

> Create valid organon files with correct placement and references.

### Goal

Produce a valid organon file with proper frontmatter, structure, and inheritance.

### Steps

1. **Classify scope.** Product, domain, feature, or component.
2. **Check parent.** Load parent ETHOS.md, new file must inherit constraints.
3. **Select template.** ETHOS, PHILOSOPHY, PROTOCOL, or README.
4. **Determine placement.** Under organon/domains/ or organon/features/.
5. **Generate frontmatter.** Type, scope, name, version, summary, token_estimate.
6. **Write content.** Follow template section structure.
7. **Validate.** `organon validate` on new file.
8. **Check references.** `organon verify --gate triplets` for bidirectional refs.

### Verification

- [ ] `organon validate` passes
- [ ] File in correct directory
- [ ] Parent constraints inherited

---

## PROTO-ORG-7: Quality Review

> Semantic review beyond automated gates.

### Goal

Find quality issues automation can't detect: vague invariants, misordered principles, inconsistent terminology.

### Steps

1. **Run automated gates.** `organon verify` and `organon validate`.
2. **Review invariants.** Testable? Specific? Enforceable?
3. **Review principles.** Genuinely prioritized? Conflict test passes?
4. **Review identity.** Specific and distinguishing?
5. **Review heuristics.** Cover actual recurring decisions?
6. **Check anti-patterns.** Phantom enforcement, aspiration as invariant, etc.
7. **Generate report.** Findings by severity with fix recommendations.

### Verification

- [ ] All automated gates pass
- [ ] Every invariant passes "testable?" filter
- [ ] No anti-pattern matches
