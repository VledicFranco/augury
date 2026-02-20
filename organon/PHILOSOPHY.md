---
type: rationale
scope: product
name: augury
version: "1.0"
summary: Design rationale for Augury — why the methodology uses normalized metrics, dependency graphs, priority algorithms, and convergence strategies
token_estimate: 2800
decision_count: 11
inherits_from: []
load_priority: medium
audience: [llm, human]
---

# Augury — Philosophy

> Explains WHY Augury is designed the way it is. The constraints live in ETHOS.md; this document is the reasoning behind them.

---

## The Problem

Organizations accumulate dysfunction faster than they resolve it. An engineering team at any given moment has dozens of things wrong — missing CI/CD, no code review, single points of failure, unclear requirements, communication bottlenecks, security gaps — and limited capacity to fix them. The standard responses are:

1. **Reactive whack-a-mole.** Fix whatever's loudest. No measurement, no prioritization, no awareness of what fixing one thing unblocks. Feels productive, rarely is.
2. **Waterfall strategy.** Write a long plan. Execute it linearly. Discover halfway through that the world changed. Abandon the plan; return to whack-a-mole.
3. **Intuition-driven prioritization.** The leader picks what "feels" most important. Sometimes correct, often biased toward what's visible over what's structural. No mechanism for others to verify or challenge the ordering.

All three fail for the same reason: they lack a **formal model** connecting problems to measurements to solutions to dependencies. Without that model, you can't compute priority — you can only guess at it.

---

## The Bet

**Bet:** Organizational health can be modeled as a set of normalized metrics (HPMs) connected to classified challenges and solution dependency graphs, and this model produces better execution ordering than intuition alone.

**Why this works:**
- **Normalization creates comparability.** When CI pipeline coverage (H1) and developer satisfaction (H6) are both 0→1, you can compare them, aggregate them, and compute priority across domains. Without normalization, you're comparing apples to architecture diagrams.
- **Dependency graphs reveal hidden value.** The most important work in a neglected organization is often the least glamorous — setting up CI, establishing branch protection, defining contracts. These are enablers that unblock everything else. A priority algorithm that accounts for downstream impact surfaces this; intuition frequently buries it.
- **Explicit formulas are auditable.** When the priority algorithm is written down, anyone can verify the ordering, challenge the inputs, or propose a different formula. Intuition-based prioritization is a black box.
- **Real-world validation.** This methodology was extracted from a live CTO assessment: 22 challenges, 46 solutions, 28 HPMs across 5 domains. The model produced a clear execution plan with convergence points that intuition alone would not have identified (specifically: routing CI/CD, infra-dev contract, and deployment patterns through a single pilot project).

**Falsification condition:** If an organization using Augury consistently overrides the priority algorithm's ordering without degraded outcomes, the model isn't adding value over intuition. The algorithm should produce orderings that, when followed, demonstrably improve HPMs faster than ad-hoc approaches.

---

## Design Decisions

### 1. Universal 0→1 normalization

**Choice:** All HPMs must be expressed as ratios in [0, 1], where higher = healthier. No exceptions.

**Rationale:** Comparability is the foundation of everything else. If metrics use different scales (percentage, count, Likert, binary), you can't aggregate them into domain scores, can't feed them into a single priority formula, and can't visualize them on the same radar chart. The normalization transforms (ideal/actual, actual/target, 1-rate, avg/max) handle every category of natural metric while preserving a universal direction: higher is always better.

**Trade-off:** Precision loss. A 0→1 ratio compresses information — you lose the absolute scale. "0.15 CI pipeline coverage" doesn't tell you whether that's 3 out of 20 repos or 15 out of 100. The model treats both identically, which is correct for prioritization but lossy for capacity planning. Mitigation: the HPM definition fields (source, method) preserve the raw computation for anyone who needs absolute numbers.

### 2. Solutions as a DAG

**Choice:** Solution dependencies form a directed acyclic graph, not a flat list or a tree.

**Rationale:** Real dependencies aren't linear. A CI/CD pipeline (S8) enables branch protection (S10), which enables peer review (S14). But the reference model (S42) depends on the pipeline AND branch protection AND the infra contract — a diamond, not a chain. A flat list can't express "S42 needs S8, S10, and S36a." A tree can't express shared dependencies. Only a DAG captures the full structure.

The DAG also enables three critical analyses:
1. **Topological ordering** — a valid execution sequence respecting all dependencies
2. **Critical path** — the longest dependency chain, which bounds minimum time to completion
3. **Downstream count** — how many solutions each solution unblocks, which feeds the ground-zero priority formula

**Trade-off:** Complexity. A DAG is harder to reason about than a list. Cycle detection is necessary. Visualizing a 46-solution DAG requires tooling. For very small challenge spaces (< 10 solutions), a flat priority list might be sufficient — but the DAG is still valid, just trivial.

### 3. The priority algorithm

**Choice:** `priority = effort / (current_HPM + expected_HPM_change)`, with a ground-zero variant that adds a downstream multiplier.

**Rationale:** The formula encodes a specific value judgment — **we want the highest health improvement per unit of effort**. Dividing effort by impact naturally surfaces cheap-and-impactful work while deprioritizing expensive-and-marginal work. Including `current_HPM` in the denominator means that areas already close to healthy get deprioritized (diminishing returns), while areas at zero get maximum attention.

The ground-zero variant exists because when `current_HPM ≈ 0` for nearly everything (greenfield or deeply neglected), the standard formula degenerates — the current-health factor contributes nothing to differentiation. Adding `|downstream(S)|` as a multiplier correctly values foundational work in this specific scenario.

**Trade-off:** The formula is opinionated. Some organizations might value risk reduction over health improvement, or strategic alignment over raw impact. The formula is declared as parametrizable — not the only valid formula — but the methodology defaults to this one and requires any alternative to be stated explicitly. A pluggable formula would be more flexible but would lose the opinionated default that makes Augury actionable out of the box.

### 4. Enabler inheritance

**Choice:** Solutions with zero direct HPM impact inherit the average HPM impact of their direct dependents.

**Rationale:** Without inheritance, enabler solutions (evaluate CI platforms, design architecture, define API contracts) would produce division-by-zero or infinite priority scores. More importantly, they'd be invisible to the algorithm — permanently deprioritized despite being prerequisites for high-impact work.

Averaging the dependents' impact is a principled heuristic: if an enabler unblocks three solutions with HPM deltas of 0.5, 0.3, and 0.8, the enabler inherits 0.53 — proportional to the value it enables. This keeps the formula unchanged while providing a meaningful priority score.

**Trade-off:** The inherited value is an approximation. An enabler that unblocks one high-impact and two low-impact solutions gets a middling score, even though its real value is gated by the high-impact solution. A max-based inheritance would value enablers more aggressively; we chose average for conservatism.

### 5. Urgency as orthogonal axis

**Choice:** Urgency is a boolean flag processed as a separate subspace, not a multiplier in the priority formula.

**Rationale:** Urgency and priority are fundamentally different concepts. Priority asks "what delivers the most value per effort?" Urgency asks "what is time-sensitive?" A security vulnerability might have low priority (narrow HPM impact, moderate effort) but extreme urgency. If urgency were a multiplier, it would distort the priority ordering by mixing temporal pressure with structural value.

The two-subspace approach preserves the algorithm's integrity within each subspace while respecting time constraints: urgent solutions are processed first, but their internal ordering still follows the priority algorithm.

**Trade-off:** Binary urgency is blunt. Real urgency has degrees — a compliance deadline in 2 weeks is more urgent than one in 6 months, but both are flagged identically. A tiered urgency system (critical/high/medium) would add precision but also complexity. We chose simplicity because urgency should be rare — if most solutions are flagged urgent, the flag has lost meaning.

### 6. Strict hierarchy (Domain → Area → Challenge → Solution)

**Choice:** Every challenge belongs to exactly one area, every area belongs to exactly one domain. No multi-homing.

**Rationale:** Classification forces clarity. If a challenge can belong to multiple areas, it's either too broad (needs decomposition) or the areas are poorly defined. Forcing single-homing creates pressure to define areas precisely. Solutions, by contrast, CAN have cross-domain HPM impact — this is intentional. The classification constraint applies to the problem space (challenges), not the solution space.

**Trade-off:** Sometimes a challenge genuinely spans areas. "Developer burnout from structural problems" could be Dev Experience (burnout area) or Org Structure (workload area). The framework forces a choice, which may feel arbitrary. The mitigation is that solutions connected to that challenge can still impact HPMs in other areas — the cross-domain impact mechanism captures the spillover even when the challenge itself is classified in one place.

### 7. Convergence as strategy pattern

**Choice:** Augury explicitly supports convergence — routing multiple solutions through a single pilot project.

**Rationale:** Most strategy frameworks treat solutions as independent work items. But in practice, the highest-leverage move is often a pilot project that validates multiple patterns simultaneously. A single HR app build that proves CI/CD, infra-dev contract, and deployment patterns is worth 3x the investment of three separate initiatives — because the reference model it produces makes rollout dramatically cheaper.

Convergence is where the DAG and the priority algorithm intersect with execution reality. The DAG shows which solutions share dependencies; the priority algorithm identifies which are most valuable; convergence identifies which can be validated together.

**Trade-off:** Convergence adds conceptual complexity. Not every organization needs it — for small challenge spaces, sequential execution of the priority-ordered list is simpler and sufficient. Convergence also creates coupling risk: if the pilot project fails, all converged solutions are blocked. The mitigation is that convergence is optional — a strategy pattern, not a requirement.

### 8. Explicit formula declaration

**Choice:** Every strategy must state which priority formula it uses. No implicit default.

**Rationale:** The priority algorithm is parametrizable — the standard formula works for most scenarios, the ground-zero variant for neglected organizations, and other formulas may emerge for different contexts (risk-weighted, strategic-alignment-weighted). Forcing explicit declaration prevents silent assumptions. When someone asks "why is S42 higher priority than S14?", the answer must be traceable to a stated formula with visible inputs.

**Trade-off:** Overhead. For a simple challenge space where the standard formula is obviously correct, requiring explicit declaration feels like bureaucracy. But the cost of stating the formula is one line; the cost of an implicit assumption producing incorrect ordering is a misallocated quarter.

---

## Trade-offs

| Decision | Benefit | Cost |
|----------|---------|------|
| Universal 0→1 normalization | Comparability, aggregation, single priority formula | Precision loss — absolute values compressed |
| Solutions as a DAG | Captures real dependencies, enables critical path and downstream analysis | Complexity — requires tooling for visualization and cycle detection |
| Priority algorithm with ground-zero variant | Opinionated default that works out of the box, values foundational work | Formula is one opinion among many — may not fit every context |
| Enabler inheritance (average of dependents) | Prevents enablers from being invisible to the algorithm | Approximation — averaging may under-value enablers of high-impact work |
| Urgency as orthogonal axis | Preserves algorithm integrity, separates temporal from structural value | Binary urgency is blunt — no degrees of time-sensitivity |
| Strict hierarchy | Forces precise classification, prevents ambiguous multi-homing | Some challenges genuinely span areas — forced choice may feel arbitrary |
| Convergence pattern | Maximizes impact per effort through shared pilots | Adds conceptual complexity, creates coupling risk in pilot projects |
| Explicit formula declaration | Traceability, auditability, prevents silent assumptions | Minor overhead of stating the formula for simple cases |
