# Example — ETL synchronization

## Input story

> Every night, synchronize active customers from Oracle into PostgreSQL. New customers must be inserted and existing customers updated.

Original criteria:

- `AC-01` Job runs nightly.
- `AC-02` Active customers are synchronized.
- `AC-03` Existing customers are updated.

## What a shallow planner might produce

- Test new customer.
- Test existing customer.
- Test database error.
- Test empty values.

This is insufficient because it does not model the data flow or execution semantics.

## Example change model

```text
Oracle
  -> paginated extraction
  -> transformation/mapping
  -> upsert
  -> PostgreSQL
```

Relevant discovered dimensions:

- source can mutate during extraction;
- Oracle and PostgreSQL use different null/type constraints;
- execution can be restarted;
- scheduler may launch overlapping runs;
- existing destination rows may represent customers no longer active;
- large volume makes pagination/batching correctness relevant.

## Sample derived criteria

### DV-001 [DATA] [HIGH]

**Scenario**  
An Oracle row contains a nullable source field mapped to a PostgreSQL `NOT NULL` column.

**Why this matters**  
A single incompatible record may abort a batch or be silently omitted depending on implementation.

**Expected behavior**  
Requires evidence from mapping/rejection policy. If none exists, mark as clarification rather than inventing a fallback value.

**Evidence**  
Source/destination schemas have different nullability.

**Suggested validation**  
Integration test with a representative incompatible row plus verification of persisted rows and diagnostics.

### DV-002 [CONC] [HIGH]

**Scenario**  
Two scheduler instances start the same synchronization concurrently.

**Why this matters**  
Concurrent upserts can create duplicates, lock contention, overwrites, or misleading run status depending on keys and execution coordination.

**Expected behavior**  
Derived from existing uniqueness/locking/idempotency design; otherwise clarification required.

**Suggested validation**  
Run two workers against the same controlled dataset and reconcile destination state.

### DV-003 [FAIL] [HIGH]

**Scenario**  
The process stops after 60% of the batches are committed, then the job runs again.

**Why this matters**  
This tests restart safety and whether replay produces a consistent final state.

**Expected behavior**  
Final destination should satisfy established synchronization invariants without duplicate logical customers.

**Suggested validation**  
Fault injection after a deterministic batch followed by restart and full reconciliation.

### DV-004 [DATA] [HIGH]

**Scenario**  
Source rows change while paginated extraction is in progress.

**Why this matters**  
Offset-based or unstable ordering can skip or duplicate rows if the source dataset mutates.

**Expected behavior**  
Depends on the consistency guarantee intended by the ETL. If snapshot semantics are undefined, record an ambiguity.

**Suggested validation**  
Modify controlled source rows between pages and compare extracted logical identities.

### AMB-001

**Undefined behavior**  
What should happen in PostgreSQL when a previously active Oracle customer becomes inactive?

**Plausible interpretations**  
Delete destination row, mark inactive, retain unchanged, or exclude only from future inserts.

**Risk of choosing incorrectly**  
Destination can diverge permanently from business expectations.

**Decision required**  
Define synchronization semantics for active -> inactive transitions.

## Why this example matters

The useful cases were derived from the ETL's execution model, schemas, consistency semantics, and restart behavior—not from a universal list of HTTP edge cases.