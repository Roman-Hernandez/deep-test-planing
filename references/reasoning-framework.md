# Reasoning Framework

This reference defines how the skill should reason before generating any test plan.

## Goal

Produce context-sensitive validation thinking, not generic test checklists.

The agent must first understand what is changing, how the system behaves, and what evidence exists. Only then should it generate additional validation criteria.

## 1. Build an evidence map

Classify evidence into:

- Requirement evidence: user story, issue, acceptance criteria, bug report, product notes.
- Implementation evidence: source code, changed files, diff, configuration.
- Behavioral evidence: tests, examples, fixtures, logs, existing outputs.
- Contract evidence: schemas, interfaces, API contracts, events, database constraints.
- Architectural evidence: module boundaries, dependency direction, ownership of responsibilities.

Do not treat absence of evidence as evidence of absence.

## 2. Identify the change shape dynamically

Do not force predefined feature types. Infer the dominant behavior from the evidence.

Possible shapes include, but are not limited to:

- transformation;
- calculation;
- workflow;
- state transition;
- extraction/load;
- scheduling;
- report generation;
- integration;
- event consumption/production;
- data migration;
- caching;
- bug correction;
- refactor;
- configuration/infrastructure-sensitive behavior.

A change may have multiple shapes at once.

## 3. Model the system around the change

Construct a concise model with only relevant dimensions.

### Inputs

What enters the behavior?

This may be:

- user input;
- files;
- events;
- database rows;
- API responses;
- environment/configuration;
- clock/time;
- scheduled triggers;
- previous state.

### Processing

What transformations, decisions, calculations, validations, or side effects occur?

### Outputs

What does the system produce or mutate?

### Dependencies

What external or internal dependencies influence correctness?

### State

What state exists before and after the behavior?

### Invariants

What must remain true regardless of execution path?

Examples:

- no duplicate logical record;
- totals remain reconcilable;
- invalid state transitions never occur;
- data is not silently lost;
- exactly-once side effect semantics are preserved when promised by the system.

Do not invent invariants. Derive them from domain evidence, constraints, or established behavior.

## 4. Reason from failure mechanisms, not canned values

Instead of mechanically checking `null`, `empty`, `max`, etc., ask what can make this system wrong.

Examples:

- precision loss;
- stale state;
- ordering;
- partial writes;
- duplicate execution;
- inconsistent snapshot;
- incompatible schema;
- race condition;
- retry after unknown outcome;
- truncation;
- timezone boundary;
- hidden shared dependency;
- unhandled legacy value;
- memory pressure;
- unexpected downstream interpretation.

Then derive concrete scenarios from the actual system.

## 5. Expand along four axes

For every important behavior, explore these axes when relevant:

### Variation

What values, states, datasets, configurations, roles, or environmental conditions materially change behavior?

### Sequence

What changes when actions happen in a different order, are repeated, resumed, retried, delayed, or interrupted?

### Interaction

What combinations of otherwise-valid conditions create risk?

### Propagation

What else can be affected beyond the directly changed component?

These four axes are general enough to apply to CRUD, ETLs, reports, algorithms, schedulers, event systems, migrations, and bug fixes.

## 6. Distinguish certainty levels

Every derived statement should fall into one of these classes:

- Known requirement.
- Derived expectation.
- Risk hypothesis.
- Ambiguity.

Never state an uncertain behavior as if product already decided it.

## 7. Seek counterexamples

For every major assumption, search for a plausible counterexample.

Examples:

- assumption: source data is stable during processing;
- counterexample: records change while pagination is in progress.

- assumption: retry is harmless;
- counterexample: side effect committed but response timed out.

- assumption: bug is isolated;
- counterexample: fix touches a shared helper used by six other features.

The skill should prefer counterexamples that are plausible in production, not merely theoretically possible.

## 8. Avoid checklist inflation

Do not reward the number of generated cases.

A scenario is valuable if it covers a materially different:

- failure mechanism;
- business rule;
- invariant;
- state transition;
- data characteristic;
- dependency behavior;
- regression path;
- risk interaction.

If a new case is merely a cosmetic variation of an existing case, merge or omit it.

## 9. Produce traceable reasoning

The final plan should let a developer answer:

- Why is this scenario here?
- What evidence suggested it?
- What could break if it fails?
- Is the expected behavior known or still ambiguous?
- What is the cheapest reliable way to validate it?

If those questions cannot be answered, the criterion is probably too generic.