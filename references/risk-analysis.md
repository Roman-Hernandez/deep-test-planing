# Risk Analysis

Risk analysis prioritizes validation effort without turning the plan into a simplistic severity scorecard.

## Risk model

A scenario becomes important when one or more of these are true:

- failure corrupts or loses data;
- failure affects money, permissions, identity, compliance, or irreversible state;
- failure can remain silent;
- failure affects many users or downstream systems;
- failure is difficult to recover from;
- failure is plausible under production conditions;
- failure is caused by concurrency, retries, ordering, or partial completion;
- failure crosses a trust or ownership boundary;
- failure impacts shared code or broad regression surface;
- failure can create false success while work is incomplete.

## Risk dimensions

Use qualitative judgment over rigid arithmetic.

### Impact

What happens if this fails?

Consider:

- data loss/corruption;
- financial inconsistency;
- security exposure;
- incorrect business decisions;
- broken downstream processing;
- unavailable functionality;
- operator confusion;
- expensive repair or replay.

### Likelihood

How plausible is the triggering condition in the real system?

Do not equate rarity with impossibility. Events such as timeout-after-commit, duplicate delivery, stale reads, or partial retries may be uncommon but are normal distributed-system conditions.

### Detectability

Would the problem be visible immediately, or could it remain silent?

Silent omissions and false success deserve extra attention.

### Recoverability

Can the system safely retry, repair, replay, reconcile, or roll back?

Irreversible and hard-to-repair failures deserve deeper validation.

### Blast radius

Is this isolated to one request or shared across batches, tenants, reports, consumers, or common helpers?

## Suggested qualitative levels

Use `HIGH`, `MEDIUM`, or `LOW` only when useful.

### HIGH

Failure can cause material corruption, security/financial impact, broad regression, silent loss, or difficult recovery; or the scenario targets a known fragile mechanism.

### MEDIUM

Failure is meaningful but bounded, diagnosable, and recoverable.

### LOW

Failure has limited impact and easy recovery, but the scenario still adds distinct validation value.

Do not downgrade a scenario merely because it is an edge condition.

## Prioritization rule

Prioritize by failure mechanism, not by how easy the test is to write.

A difficult concurrency or replay scenario may be more important than ten trivial validation cases.

## Unknown risk

When repository evidence is incomplete, explicitly mark uncertainty rather than assigning false precision.

Example:

```text
Risk: UNKNOWN / potentially HIGH
Reason: The story introduces a retry path, but the downstream operation's idempotency guarantees are not documented.
```

## Risk concentration

After generating criteria, identify clusters where several risks share one mechanism.

Examples:

- retry/idempotency;
- time boundaries;
- data reconciliation;
- shared date parser;
- schema compatibility;
- concurrency around uniqueness;
- partial distributed transactions.

These clusters belong in the final `Highest-risk areas` section because they often indicate where developers should concentrate automated tests and exploratory validation.