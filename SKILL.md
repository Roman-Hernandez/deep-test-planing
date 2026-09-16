---
name: deep-test-planning
description: Generate an exhaustive, evidence-backed developer test plan from a user story, issue, bug, change request, or implementation context. Expands beyond explicit acceptance criteria by investigating the actual system, deriving hidden validation scenarios, regression risks, ambiguities, and engineering-quality concerns. Use before handing development work to QA. Works across feature types including APIs, ETLs, reports, batch jobs, event-driven systems, data pipelines, bug fixes, frontend flows, algorithms, migrations, integrations, infrastructure-sensitive changes, and other software changes.
---

# Deep Test Planning

## Purpose

Turn a user story, issue, bug report, change request, or implementation into a deep developer-facing test plan.

The primary output is NOT a PASS/FAIL verdict and this skill does NOT replace human QA.

The primary output is an exhaustive, evidence-backed Test Plan that expands the original acceptance criteria with important scenarios the specification may have omitted.

Core principle:

> Acceptance criteria describe the explicitly requested behavior. They are the starting point, not the complete validation space.

Optimize for finding what everyone forgot to ask.

## Supporting references

Use the supporting references progressively. Do not load or apply them as static checklists.

- `references/reasoning-framework.md` — core reasoning model. Read first when performing a deep analysis.
- `references/requirement-expansion.md` — how to expand finite requirements without inventing product rules.
- `references/risk-analysis.md` — risk prioritization and concentration.
- `references/regression-analysis.md` — diff/repository-aware regression reasoning.
- `references/architecture-quality.md` — Clean Code, SOLID, maintainability, and architecture review without dogma.
- `references/test-plan-generation.md` — how to compose the final developer-facing artifact.
- `templates/test-plan.md` — reusable output skeleton.

Examples under `examples/` are calibration material, not scenarios to copy mechanically.

## Non-negotiable rules

1. Do not treat the supplied acceptance-criteria checklist as exhaustive.
2. Do not generate cases by blindly applying a static checklist.
3. Do not assume the change is CRUD, HTTP, frontend, backend, or any other predefined shape.
4. First understand the requested behavior and, when repository context is available, investigate the affected system.
5. Derive validation dimensions dynamically from the actual domain, behavior, data flow, dependencies, states, invariants, implementation, diff, and regression surface.
6. Reference catalogs are reasoning aids, never mandatory checklists.
7. Never invent business requirements. Clearly separate known requirements, derived expectations, risks, and ambiguities.
8. Prefer high-value scenarios over mechanically generating Cartesian combinations.
9. Explore interactions between relevant conditions when combinations can reveal behavior that isolated cases cannot.
10. Do not claim the implementation is correct merely because a plan was generated or tests passed.
11. Keep functional validation separate from engineering-quality findings.
12. Every important derived criterion should have a reason for existing.

## Inputs

Use as much evidence as is actually available:

- User story / issue / bug / change request.
- Original acceptance criteria.
- Repository source code.
- Relevant tests.
- Git diff or pull request changes.
- Data models and schemas.
- Configuration.
- Architecture and dependency boundaries.
- Existing behavior and conventions.
- Logs, examples, fixtures, documentation, or contracts when relevant.

Do not fabricate unavailable context. Mark uncertainty explicitly.

## Workflow

### Phase 1 — Understand intent

Extract the actual goal of the change without prematurely designing tests.

Identify:

- requested outcome;
- actors or initiating systems when relevant;
- explicit acceptance criteria;
- known constraints;
- expected outputs and side effects;
- explicitly stated non-goals;
- unresolved language or assumptions.

Do not assume that missing behavior has an obvious business answer.

### Phase 2 — Investigate the system

When repository or implementation context exists, inspect it before expanding the test plan.

Determine what actually participates in the behavior. Follow the change through the system rather than looking only at the edited function.

Investigate as relevant:

- entry points;
- domain/business logic;
- data sources and sinks;
- transformations;
- persistence;
- state transitions;
- asynchronous work;
- integrations;
- shared components;
- existing validations;
- retries and recovery;
- scheduling/time behavior;
- caching;
- authorization/trust boundaries;
- configuration;
- existing tests;
- callers and downstream consumers.

If a diff is available, distinguish:

- what the requirement says should change;
- what the implementation actually changed;
- shared behavior touched indirectly;
- potential regression surface outside the story.

Use `references/reasoning-framework.md` to construct the evidence map and change model.

### Phase 3 — Build a change model

Create a concise internal model of the feature/change before generating scenarios.

Model only dimensions that exist or plausibly matter in this system, such as:

- behavior and rules;
- data flow;
- states and transitions;
- invariants;
- trust boundaries;
- external dependencies;
- time and ordering;
- concurrency;
- volume and scale;
- failure/recovery behavior;
- consumers and side effects.

The model must adapt to the change.

An ETL, report generator, financial calculation bug, UI workflow, Kafka consumer, migration, batch job, cache change, and API endpoint should NOT receive the same generic plan.

### Phase 4 — Expand the requirement space

Use `references/requirement-expansion.md`.

For every explicit acceptance criterion, ask:

1. What must be true for this criterion to work?
2. What implicit contracts does the surrounding system already enforce?
3. What relevant boundaries exist in the actual domain?
4. What states can the system realistically be in?
5. What happens before, during, and after the operation?
6. What dependencies can alter the outcome?
7. What failure modes can leave partial or inconsistent results?
8. What repeated, delayed, reordered, or concurrent execution could occur?
9. What data characteristics could change behavior?
10. What existing functionality could regress because of this change?
11. What security or trust assumptions are relevant?
12. What scale/performance behavior matters for this specific change?
13. What would make the system difficult to diagnose when it fails?
14. Which combinations of conditions are more dangerous than either condition alone?
15. What behavior is undefined and requires product/business clarification?

Do not force irrelevant dimensions into the plan.

### Phase 5 — Adversarial exploration

Think like a developer attempting to disprove their own assumptions.

Ask:

> If the happy path works, how could this feature still be wrong?

> What realistic sequence, state, dataset, timing, dependency behavior, or interaction could expose an assumption that nobody wrote down?

> What is the strangest plausible production condition that remains consistent with this system?

Explore meaningful interactions only when they create a new failure mechanism.

Examples:

- retry + timeout;
- concurrency + uniqueness;
- state transition + stale reads;
- timezone + boundary date;
- partial failure + retry;
- pagination + mutation;
- cache + update;
- authorization + ownership;
- transaction + external dependency;
- duplicate event + non-idempotent side effect;
- schema evolution + legacy data;
- large volume + memory/timeout constraints.

These are examples of reasoning patterns, not mandatory cases.

### Phase 6 — Regression analysis

Use `references/regression-analysis.md`.

Do not limit analysis to the story's named behavior.

Generate regression criteria only when there is a credible impact path from changed code/config/contracts to existing behavior.

### Phase 7 — Risk analysis

Use `references/risk-analysis.md` to identify which scenarios deserve the most attention.

Do not rank merely by ease of testing. Consider impact, likelihood, detectability, recoverability, and blast radius.

### Phase 8 — Engineering quality review

Use `references/architecture-quality.md`.

Keep this separate from the Test Plan's behavioral criteria.

Review only against the actual architecture and conventions of the project. Do not force Clean Architecture or unnecessary abstractions onto a system that does not use them.

Explain concrete evidence instead of outputting meaningless statements such as `SOLID: PASS`.

### Phase 9 — Produce the Test Plan

Use `references/test-plan-generation.md` and `templates/test-plan.md`.

The output must distinguish original criteria from newly derived criteria.

Use these labels where useful:

- `AC` — original acceptance criterion.
- `DRV` — behavior derived from requirement/system evidence.
- `EDGE` — boundary or unusual condition.
- `DATA` — data quality/integrity/transformation.
- `STATE` — state or transition behavior.
- `FAIL` — failure/recovery scenario.
- `INT` — integration/dependency behavior.
- `CONC` — concurrency/idempotency/ordering.
- `REG` — regression risk.
- `SEC` — security/trust boundary.
- `PERF` — performance/volume/resource behavior.
- `OBS` — observability/diagnosability.
- `ARCH` — architecture concern.
- `QUAL` — code quality/maintainability.
- `AMB` — ambiguity requiring clarification.

Do not create criteria merely to fill every label.

## Derived criterion format

Important criteria should include enough information to be actionable:

```text
DV-### [CATEGORY] [RISK]

Scenario:
What condition or behavior should be validated.

Why this matters:
Why this case exists and what assumption/risk it targets.

Expected behavior:
Known expected result, derived invariant, `Requires clarification`, or `Risk exploration — no product behavior asserted`.

Evidence:
Requirement, code path, schema, dependency, diff, existing behavior, or reasoning that supports including the case.

Suggested validation:
How a developer could validate it: unit, integration, property, contract, E2E, data reconciliation, manual inspection, fault injection, load test, etc.
```

Use risk levels only as prioritization aids, not as substitutes for reasoning.

## Requirement classification

Never silently turn an assumption into a requirement.

Classify conclusions as:

### Known requirement
Explicitly stated by the story, issue, contract, or established system behavior.

### Derived expectation
Strongly implied by system invariants, existing contracts, or implementation behavior. State the evidence.

### Risk hypothesis
A plausible failure mode worth testing, without claiming a business rule.

### Ambiguity
Expected behavior cannot be determined safely. Request clarification and explain the consequence of leaving it undefined.

## Coverage depth

Do not optimize for a specific number of cases.

Ten deeply relevant criteria can be better than one hundred generic cases. Conversely, a complex distributed or data-heavy change may legitimately require a very large plan.

Continue expanding while new cases cover materially different:

- behaviors;
- failure mechanisms;
- invariants;
- state transitions;
- data characteristics;
- dependency interactions;
- regression paths;
- risk combinations.

Stop generating variants when they add no meaningful new information.

## Test strategy recommendations

For each scenario, recommend the cheapest validation layer that can reliably prove the behavior.

Examples include:

- unit tests;
- table-driven tests;
- property-based tests;
- integration tests;
- contract tests;
- component tests;
- end-to-end tests;
- reconciliation queries;
- golden/snapshot tests;
- fault injection;
- concurrency/race tests;
- migration verification;
- performance/load tests;
- manual exploratory validation.

Do not default everything to E2E.

## Final behavior

The developer should receive a plan substantially deeper than the original checklist, with traceable reasons for the additional scenarios.

The goal is not to replace QA or guarantee correctness.

The goal is to make development hand QA an implementation that has already been challenged against a much larger and more realistic behavior space.