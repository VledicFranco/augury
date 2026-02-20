---
type: navigation
scope: product
name: augury
version: "1.0"
summary: Open-source methodology and application for organizational health diagnosis and strategic execution
token_estimate: 1500
---

# Augury

**An open-source methodology and application for organizational health diagnosis and strategic execution.**

Augury reads the signs. It provides a structured framework for assessing organizational health through normalized metrics, identifying challenges, mapping solutions as a dependency graph, and computing optimal execution paths.

---

## The Problem

Organizations accumulate problems faster than they solve them. Engineering teams know things are broken — no CI/CD, no code review, single points of failure, burnout — but lack a systematic way to:

1. **Measure** how broken things are (and how they improve over time)
2. **Connect** problems to their root causes across domains
3. **Prioritize** which solutions to pursue first, considering dependencies and effort
4. **Converge** multiple improvements through shared pilot projects to maximize impact

Most teams either fix things ad-hoc (reactive whack-a-mole) or create elaborate plans that don't survive contact with reality (waterfall strategy). Augury occupies the middle ground: structured enough to be rigorous, adaptive enough to evolve as you learn.

## The Model

Augury's data model has four core entities:

```
Domain → Area → Challenge → Solution[]
                    ↓              ↓
                  HPMs          HPM Impact
                (affected)     (expected Δ)
```

### HPMs — Health & Performance Metrics

Every aspect of organizational health is measured as a **normalized 0→1 metric** called an HPM.

| Field | Purpose |
|-------|---------|
| **Metric** | A ratio definition (e.g., "repos with CI / total repos") |
| **Source** | Where the data comes from (GitHub API, CI platform, survey, manual audit) |
| **Method** | How to compute it |
| **Frequency** | How often to measure (weekly, monthly, quarterly) |
| **Current** | Baseline value (often 0 for greenfield assessments) |
| **Target** | Goal value (the "healthy" state) |

**Normalization transforms** ensure all metrics are comparable:
- Counts that should decrease: `ideal / actual`
- Counts that should increase: `actual / target`
- Rates that should decrease: `1 - rate`
- Subjective measures: `avg_score / max_score`

### Challenges

An identified organizational problem, classified by **domain** (top-level category) and **area** (subcategory). Each challenge affects one or more HPMs.

### Solutions

A proposed action that addresses one or more challenges. Solutions have:

- **Effort** — estimated on a [0, 5) scale (0=trivial, 1=days, 2=weeks, 3=months, 4=quarters)
- **HPM Impact** — expected delta per affected HPM
- **Dependencies** — other solutions that must complete first (forms a DAG)
- **Urgency** — boolean flag for time-sensitive work (security, compliance, blocking)
- **State** — proposed → selected → in_progress → completed

### The Priority Algorithm

```
priority = effort / (current_HPM + expected_HPM_change)
```

Lower score = higher priority. For **ground-zero scenarios** (most HPMs ≈ 0):

```
priority = effort / (expected_HPM_change × (1 + |downstream(S)|))
```

Where `|downstream(S)|` is the count of solutions transitively unblocked — this values foundational work that enables everything else.

**Enabler solutions** (zero direct HPM impact but unblock others) inherit the average HPM impact of their dependents.

### Convergence Strategy

Rather than executing solutions independently, Augury supports **convergence** — routing multiple improvements through a single pilot project that simultaneously:

- Delivers business value
- Validates patterns (CI/CD, testing, infra standards)
- Creates a replicable reference model
- Then rolls out proven patterns to the rest of the organization

---

## How It Works

1. **Assess** — Survey the organization. Identify domains, areas, challenges. Define HPMs with measurement methods.
2. **Measure** — Establish baselines. Most greenfield assessments start near 0.
3. **Map** — Connect challenges to solutions. Define effort, HPM impact, and dependencies.
4. **Prioritize** — Run the priority algorithm. Identify the critical path through the solution DAG.
5. **Converge** — Find pilot projects that can carry multiple solutions simultaneously.
6. **Execute** — Work through solutions in priority order, respecting dependencies.
7. **Re-measure** — Track HPMs on cadence. Re-prioritize as the landscape shifts.

---

## Roadmap

- [ ] Formalize the methodology (Organon: ETHOS, PHILOSOPHY, PROTOCOLS)
- [ ] Define the data model schema
- [ ] Build the CLI for assessment management
- [ ] Build the dashboard (HPM radar, solution DAG visualization, phase timeline)
- [ ] Auto-measurement integrations (GitHub API, CI platforms, surveys)
- [ ] Priority algorithm engine with dynamic recalculation

---

## Origin

Augury was born from a real CTO Week 1 assessment: 22 challenges, 46 solutions, 28 HPMs across 5 domains. The framework proved useful enough to extract into a general-purpose methodology.

The name comes from the ancient practice of reading signs to understand truth and guide decisions — which is exactly what Augury does with organizational health metrics.

---

## License

MIT — see [LICENSE](LICENSE).
