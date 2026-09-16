# Test Plan Generation

This reference defines how to turn the analysis into a useful developer-facing artifact.

## Goal

The plan should be concrete enough that a developer can turn it into tests, checks, experiments, or implementation clarifications without redoing the entire analysis.

It should be deeper than the original story but remain traceable and non-dogmatic.

## Required qualities

A strong plan is:

- evidence-backed;
- specific to the actual change;
- explicit about uncertainty;
- prioritized by real risk;
- separated from architecture/code-quality findings;
- free of generic filler;
- actionable.

## Recommended structure

```markdown
# Deep Test Plan

## 1. Change understanding
## 2. Evidence inspected
## 3. Original acceptance criteria
## 4. Change/system model
## 5. Derived validation criteria
## 6. Regression surface
## 7. Ambiguities and decisions required
## 8. Suggested automated test strategy
## 9. Manual/exploratory validation candidates
## 10. Engineering quality review
## 11. Highest-risk areas
## 12. Coverage notes
```

Omit sections that do not apply.

## Change understanding

Summarize:

- requested behavior;
- affected domain;
- relevant constraints;
- what is explicitly out of scope if known.

Do not simply repeat the user story verbatim.

## Evidence inspected

List only evidence actually inspected.

Example:

```text
- HU BET-1842
- internal/report/service.go
- internal/report/repository.go
- migrations/2026_09_10.sql
- tests/report_service_test.go
- diff main...feature/BET-1842
```

This allows readers to judge confidence.

## Original acceptance criteria

Preserve original criteria separately from derived criteria.

Example:

```text
AC-01 [ORIGINAL] Include completed transactions.
AC-02 [ORIGINAL] Produce XLSX output.
```

Do not rewrite derived behavior into this section.

## Change/system model

Represent the relevant system simply.

Examples:

```text
Oracle -> query -> aggregation -> XLSX -> object storage
```

or:

```text
Event -> consumer -> domain calculation -> DB -> emitted event
```

Document important state, data, invariants, and dependencies only when relevant.

## Derived validation criteria

Group by mechanism/domain rather than forcing fixed categories.

Example groups:

- Calculation boundaries
- Data reconciliation
- Retry/recovery
- Concurrent execution
- State transitions
- Output compatibility

Each important criterion should use this structure:

```text
DV-### [CATEGORY] [RISK]
Scenario:
...

Why this matters:
...

Expected behavior:
...

Evidence:
...

Suggested validation:
...
```

### Expected behavior rules

Use one of:

- explicit expected behavior from requirements;
- derived expectation backed by invariant/contract;
- `Requires clarification`;
- `Risk exploration — no product behavior asserted`.

Never fabricate expected behavior merely to make the criterion look complete.

## Regression surface

Show the path from changed code to potentially affected existing behavior.

Prefer:

```text
Shared helper changed
 -> customer export uses helper
 -> historical export format may change
```

over:

```text
Check regressions.
```

## Ambiguities

Each ambiguity should explain:

- what is undefined;
- plausible interpretations;
- consequence of choosing incorrectly;
- what decision is needed.

## Automated test strategy

Recommend test layers by value and cost.

Example:

```text
Unit / property tests:
- rounding invariant
- date-boundary calculations

Integration tests:
- Oracle -> transformation mapping
- transaction rollback behavior

Concurrency test:
- duplicate job execution

Reconciliation:
- report total equals source-domain total
```

Avoid recommending E2E for behavior that is better proven by a smaller test.

## Manual/exploratory candidates

Reserve for scenarios that benefit from human observation or are expensive to automate.

Examples:

- generated report readability/formatting;
- unusual workflow usability;
- operator diagnostics;
- visual layout across realistic content lengths.

## Engineering quality review

Use findings from `architecture-quality.md` and keep them distinct from test criteria.

## Highest-risk areas

Summarize risk clusters, not individual trivial cases.

Example:

```text
1. Idempotency after unknown external outcome
2. Reconciliation when source changes during extraction
3. Shared date helper regression around month boundaries
```

## Coverage notes

State important limits of the plan.

Examples:

- repository code was unavailable;
- production schema was not available;
- no diff was provided;
- external provider contract was inferred only from interface definitions;
- performance thresholds are undefined.

This protects the plan from false confidence.