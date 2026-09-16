# Deep Test Planning

An agent-agnostic software engineering skill for generating **deep, evidence-backed developer test plans** from user stories, issues, bug reports, change requests, diffs, and repository context.

## Why?

Acceptance criteria are intentionally finite. Production behavior is not.

A story may say what the product expects, but it rarely describes every relevant state, data characteristic, failure mode, timing interaction, dependency behavior, regression path, or unusual production condition.

`deep-test-planning` helps a coding agent investigate the actual system and expand a story into a much richer validation space **before the implementation is handed to QA**.

It is not a replacement for QA and it is not primarily a test executor.

Its main artifact is a **Deep Test Plan**.

## Core idea

```text
User Story / Issue / Bug
          |
          v
   Understand intent
          |
          v
 Investigate repository
          |
          v
   Model the change
          |
          v
 Requirement expansion
          |
          v
 Adversarial reasoning
          |
          v
 Regression analysis
          |
          +--------------------+
          |                    |
          v                    v
    Deep Test Plan      Engineering Quality
          |                    |
          +----------+---------+
                     v
                  Developer
                     |
                     v
                     QA
```

## Not a static QA checklist

The skill deliberately does **not** assume that a change is CRUD or even an API feature.

It is intended to reason about very different changes, including:

- ETLs and data pipelines
- report generation
- bug fixes
- financial or domain calculations
- batch jobs and schedulers
- frontend workflows
- APIs
- event-driven systems
- queues and consumers
- migrations
- integrations
- caching
- algorithms
- data transformations
- infrastructure-sensitive changes
- refactors and shared-component changes

The system being changed determines the validation dimensions.

## Philosophy

> Acceptance criteria are the starting point, not the complete validation space.

> Reference catalogs are reasoning aids, not mandatory checklists.

> Never invent business requirements.

> Optimize for finding what everyone forgot to ask.

## Example

A story might contain only:

```text
Generate the monthly sales report as an Excel file.

Acceptance criteria:
- Include completed sales.
- Generate an XLSX file.
- Upload the report to storage.
```

A deep plan may discover relevant questions around:

- monetary precision and rounding;
- month/timezone boundaries;
- empty datasets;
- very large datasets;
- cancelled transactions;
- data changing during report generation;
- duplicate executions;
- retry after upload timeout;
- Excel limits;
- partial generation failures;
- storage failures;
- reconciliation between source totals and report totals;
- existing reports affected by shared generator changes;
- observability when records are omitted;
- architectural coupling introduced by the implementation.

These are not automatically assumed requirements. Each is classified and supported by evidence or presented as a risk/ambiguity.

## Skill

The initial skill definition lives in [`SKILL.md`](./SKILL.md).

Planned supporting material:

```text
references/
  reasoning-framework.md
  requirement-expansion.md
  risk-analysis.md
  regression-analysis.md
  architecture-quality.md
  test-plan-generation.md

templates/
  test-plan.md

examples/
  etl.md
  report-generation.md
  bug-fix.md
  complex-feature.md
```

## Status

Early development (`v0.1`).

The next milestone is to build the reasoning references and evaluate the skill against deliberately different stories so that it does not overfit to conventional CRUD/API development.

## Compatibility goal

The project is intentionally agent-agnostic. The core reasoning should not depend on a single coding agent or programming language.

## License

License will be added before the first stable release.