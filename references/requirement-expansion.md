# Requirement Expansion

This reference defines how to turn a finite story or issue into a broader validation space without inventing product behavior.

## Principle

Acceptance criteria express explicit expectations. The planner must discover additional conditions required to validate the behavior safely in the actual system.

The goal is not to produce more requirements. The goal is to expose what should be validated, what is implied, what is risky, and what remains undefined.

## Expansion procedure

For each explicit criterion:

1. Restate the behavior in operational terms.
2. Identify its preconditions.
3. Identify data and state dependencies.
4. Identify side effects and downstream consequences.
5. Identify invariants that can be broken.
6. Identify execution boundaries.
7. Identify failure points.
8. Identify repetition, retry, ordering, and concurrency concerns.
9. Identify interaction with existing behavior.
10. Identify unknowns that prevent a deterministic expected result.

## Expansion lenses

Use these as lenses, not mandatory sections.

### Domain behavior

Ask:

- What business or domain rule is actually being enforced?
- What neighboring rule can conflict with it?
- What special state makes the same input behave differently?
- What rule is encoded elsewhere but applies here implicitly?

### Data semantics

Ask:

- What data characteristics change the outcome?
- Are values interpreted differently across storage, service, and output layers?
- Can precision, encoding, timezone, locale, ordering, truncation, or schema shape matter?
- Can legacy records violate newer assumptions?
- Is missing, partial, duplicated, stale, or inconsistent data realistic?

### Lifecycle/state

Ask:

- What states can exist before execution?
- Which transitions are legal?
- What happens when the operation is repeated after success, partial success, or failure?
- Can old or stale state be observed?

### Time/order

Ask:

- Does date/time alter behavior?
- Do boundary periods matter?
- Can execution overlap?
- Can messages, rows, or operations arrive out of order?
- Can the state change while processing occurs?

### Dependency behavior

Ask:

- Which dependencies can fail independently?
- What if the dependency succeeds but the caller cannot observe the success?
- What if latency is extreme?
- What if the dependency returns technically valid but semantically surprising data?

### Scale/resource pressure

Ask only when scale is relevant:

- What happens at realistic high volume?
- Is the implementation bounded in memory?
- Are pagination, batching, streaming, timeouts, or output-size limits involved?
- Does load alter correctness, not just speed?

### Security/trust

Ask only when a trust boundary exists:

- Who is allowed to initiate or observe the behavior?
- Can identifiers or state be manipulated across ownership boundaries?
- Are secrets, sensitive outputs, or privileged side effects involved?

### Diagnosability

Ask:

- If a subset fails, can the operator know which subset?
- Can silent data loss occur?
- Are retries distinguishable from first attempts?
- Can success be falsely reported while downstream work failed?

## Interaction expansion

After individual risks are identified, inspect pairwise or small combinations that create new failure mechanisms.

Examples:

- retry + external side effect;
- pagination + concurrent mutation;
- timezone + monthly boundary;
- uniqueness + concurrency;
- schema evolution + legacy data;
- partial write + retry;
- cache + stale authorization;
- duplicate event + non-idempotent consumer.

Do not generate combinations unless the interaction creates behavior not covered by the individual cases.

## Ambiguity handling

If the expected behavior cannot be determined from available evidence:

- do not guess;
- mark the criterion as `AMB`;
- describe the competing plausible interpretations;
- state the consequence of choosing incorrectly;
- identify who or what source should resolve it when possible.

## Traceability

Each derived criterion should trace to at least one of:

- an explicit acceptance criterion;
- observed implementation behavior;
- an invariant;
- a schema or contract;
- a dependency interaction;
- a regression path;
- a plausible failure mechanism grounded in system evidence.

Avoid unsupported creativity.