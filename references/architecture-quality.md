# Architecture and Engineering Quality Review

This reference keeps engineering-quality concerns separate from behavioral validation.

## Principle

Do not force a preferred architecture onto every repository.

Review changes against:

- the system's existing architectural style;
- the responsibility of the modified component;
- dependency direction;
- testability;
- maintainability;
- clarity of behavior;
- the cost of future change.

Clean Architecture, SOLID, and Clean Code are lenses, not religious rules.

## Architecture review

Ask only where relevant:

### Responsibility placement

- Is business/domain logic placed in an infrastructure or transport layer without reason?
- Did a handler/controller/job become responsible for orchestration, validation, persistence, transformation, and policy at once?
- Did data-access code absorb domain rules?

### Dependency direction

- Did core behavior gain a direct dependency on infrastructure details?
- Did the change create circular or surprising dependencies?
- Is a new dependency introduced where an existing boundary already exists?

### Coupling and cohesion

- Does the changed module now know too much about unrelated concerns?
- Are concepts that change together kept together?
- Was shared code created merely because two pieces of code look similar but have different reasons to change?

### Testability

- Can important business behavior be validated without excessive infrastructure setup?
- Are dependencies controllable where failure/retry behavior needs testing?
- Did the implementation hide critical logic inside static/global/unobservable behavior?

### Abstraction quality

- Was an abstraction created before there are meaningful variants?
- Does an interface express a useful contract, or merely mirror one concrete implementation?
- Did the change add unnecessary indirection?

## SOLID as diagnostic questions

### SRP

Does the changed unit have multiple unrelated reasons to change?

### OCP

Would the next realistic variant require editing a brittle conditional chain, or is the current design already sufficient?

Do not demand extensibility for hypothetical scenarios with no evidence.

### LSP

Do implementations preserve the assumptions of their abstraction, including error/side-effect semantics?

### ISP

Are consumers forced to depend on behavior they do not use?

### DIP

Does higher-level policy depend directly on lower-level details when a meaningful boundary already exists or is clearly needed for testability/change isolation?

## Clean Code review

Look for concrete maintainability problems:

- unclear names;
- functions with multiple conceptual responsibilities;
- deep branching/complexity;
- duplicated domain rules;
- hidden side effects;
- inconsistent error semantics;
- magic constants with domain meaning;
- dead code;
- comments compensating for confusing design;
- difficult-to-test logic;
- surprising mutation;
- inconsistent conventions.

Do not report stylistic preferences as defects unless the repository establishes a convention.

## Error handling quality

Check whether errors:

- retain useful context;
- distinguish expected business outcomes from system failures;
- avoid false success;
- are not silently swallowed;
- preserve retry semantics;
- avoid leaking sensitive implementation detail where relevant.

## Data and transaction quality

Where relevant:

- are transaction boundaries aligned with invariants?
- can partial state escape?
- are writes and external side effects ordered intentionally?
- does retry behavior preserve consistency?

## Quality finding format

Use concrete findings:

```text
QUAL-### [RISK]
Concern:
...

Evidence:
file/module/function or architectural path

Why it matters:
...

Suggested improvement:
...
```

Avoid meaningless summaries like:

- `SOLID: PASS`
- `Clean Architecture: FAIL`
- `Code quality: 8/10`

The objective is actionable engineering feedback, not scoring.