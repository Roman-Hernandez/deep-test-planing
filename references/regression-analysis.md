# Regression Analysis

Regression analysis determines what existing behavior may be affected even when it is not named in the story or issue.

## Principle

The requested change defines intent. The implementation defines the real blast radius.

A deep plan must inspect both.

## Regression procedure

### 1. Identify directly changed behavior

Map each requirement to the implementation areas that realize it.

### 2. Identify shared elements touched by the change

Examples:

- shared utilities;
- date/number parsing;
- serializers;
- common validators;
- repositories;
- database queries/views;
- shared models;
- schema definitions;
- middleware;
- configuration;
- common UI components;
- event contracts;
- retry helpers;
- infrastructure modules.

### 3. Find callers and consumers

Ask:

- Who calls this code?
- Who consumes this output/event/file/schema?
- Which other workflows depend on the same query/model/helper?
- Which tests reveal established behavior?

### 4. Derive credible regression paths

Do not create vague statements like `verify nothing else broke`.

Produce concrete paths:

```text
Changed shared date normalization
  -> monthly report uses same helper
  -> billing cutoff uses same helper
  -> regression criterion for cutoff around timezone boundary
```

or:

```text
Changed customer query to add new filtering
  -> ETL and admin export reuse repository method
  -> verify export still includes expected inactive records
```

## Common regression mechanisms

Use only when evidence supports them.

### Contract changes

- field renamed/removed;
- type changed;
- optional/required semantics changed;
- enum expanded;
- output ordering changed;
- event payload altered.

### Shared transformation changes

- dates;
- money/rounding;
- normalization;
- encoding;
- localization;
- mapping.

### Persistence/query changes

- filtering;
- join behavior;
- ordering;
- transaction boundaries;
- uniqueness;
- pagination;
- indexes that alter execution characteristics.

### Configuration/default changes

- timeouts;
- feature flags;
- environment values;
- default limits;
- scheduling;
- cache TTL.

### Refactoring

Refactors can preserve public behavior but still introduce regression through:

- missed branches;
- altered error propagation;
- dependency lifetime changes;
- lost side effects;
- changed ordering.

## Regression evidence

A regression criterion should state why another behavior is in scope.

Strong evidence includes:

- same modified function is called elsewhere;
- same schema is consumed elsewhere;
- same table/query supports another feature;
- diff changes a shared package;
- existing tests encode behavior potentially affected;
- dependency/interface contract changed.

Weak evidence such as `could theoretically affect anything` should not create test cases.

## Diff-aware analysis

If a diff or PR is available, compare:

- requirement scope;
- changed files;
- changed public interfaces;
- touched shared code;
- deleted behavior;
- new dependencies;
- changed configuration;
- tests added/removed.

Flag meaningful mismatches.

Example:

```text
Requirement scope: report export only
Implementation scope: shared date parser changed
Risk: regression surface is broader than stated requirement
```

## Regression output

For each meaningful regression path provide:

```text
REG-### [RISK]
Affected existing behavior:
...

Change path:
changed component -> dependency/caller -> potentially affected behavior

Why it is credible:
...

Suggested validation:
...
```

The goal is a traceable regression map, not a generic smoke-test list.