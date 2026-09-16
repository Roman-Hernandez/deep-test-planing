# Example — Financial calculation bug fix

## Input issue

> Interest is calculated incorrectly when a credit period ends in February.

Original criteria:

- `AC-01` Correct the February calculation.
- `AC-02` Do not change calculations for unaffected periods.

## Why this is not a CRUD problem

The validation space comes from date arithmetic, day-count conventions, rounding, period construction, and regression risk in shared financial logic.

## Example discovered context

Assume repository investigation shows:

- interest periods are generated from calendar dates;
- the same helper is used by multiple loan products;
- calculations round monetary values per installment;
- dates are stored without time but some calling code converts timestamps first.

## Sample derived criteria

### DV-001 [EDGE] [HIGH]

**Scenario**  
January 31 -> February 28 in a non-leap year.

**Why this matters**  
End-of-month normalization often differs from ordinary day arithmetic.

**Expected behavior**  
Must follow the established day-count convention in the domain. Do not infer a new convention from generic calendar behavior.

**Suggested validation**  
Table-driven unit test using the authoritative expected interest value.

### DV-002 [EDGE] [HIGH]

**Scenario**  
January 31 -> February 29 in a leap year.

**Why this matters**  
The fix may solve 28-day February while leaving leap-year behavior incorrect.

**Suggested validation**  
Table-driven calculation test comparing principal, rate, period, and rounded result.

### DV-003 [REG] [HIGH]

**Scenario**  
March, April, and other previously-correct periods continue producing historical results.

**Why this matters**  
The modified helper is shared and the issue explicitly requires unaffected periods to remain stable.

**Evidence**  
Shared period-calculation helper.

**Suggested validation**  
Golden regression vectors representing known historical calculations.

### DV-004 [DATA] [MEDIUM]

**Scenario**  
A timestamp close to midnight is converted into the business date before the calculation.

**Why this matters**  
Timezone conversion can move a period endpoint into a different calendar day and reproduce a seemingly-February-specific defect.

**Expected behavior**  
Use established business timezone/date semantics.

### DV-005 [EDGE] [HIGH]

**Scenario**  
A credit begins on February 29 and the next equivalent calendar date does not exist in the following year.

**Why this matters**  
This tests whether the period-building rule is explicit rather than accidentally inherited from a date library.

**Expected behavior**  
Requires domain evidence; otherwise mark as ambiguity.

## Regression path example

```text
interest issue
 -> shared period helper changed
 -> installment generation for other loan products also uses helper
 -> regression validation required outside the reported February case
```

## Key lesson

The planner should reconstruct the domain mechanics behind the bug instead of generating generic input-validation cases.