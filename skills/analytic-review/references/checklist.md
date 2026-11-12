# Code and data checklist

## 1. Structure
- Clear stages: load → validate → clean → transform → analyze → output.
- Settings (paths, thresholds, years, geographies) in one place, not scattered.
- Small single-purpose functions; no copy-pasted blocks.
- Raw data never overwritten; processed data saved separately.
- Standard layout (data/raw, data/processed, src or code, outputs).

## 2. Data in
- Row and column counts match the source; log them.
- Keys unique where they should be; duplicates found and handled explicitly.
- Types correct: IDs as strings (leading zeros), dates parsed, numerics numeric.
- Missing and impossible values (zeros, negatives, out of range) counted, with a written decision.
- Source, vintage and geography recorded.

## 3. Transformations
- **Joins:** row counts before/after match the expected relationship (1:1, m:1); unmatched keys reported; no silent many-to-many.
- **Filters:** rows dropped by each filter logged and justified.
- **Denominators:** every rate uses the right universe; no zero denominators; denominator consistent across comparisons.
- **Aggregation:** grain matches the question; no average of averages unless intended and weighted.
- **SQL:** NULL semantics in WHERE / JOIN / NOT IN; GROUP BY matches the grain; window functions partitioned and ordered correctly; DISTINCT not hiding a join problem.
- **Units and geography:** consistent units; county vs city; boundary vintage matches the data.

## 4. Results
- Totals reconcile to the source or an independent figure.
- Two or three quoted numbers recomputed independently.
- Assertions in code for every number that will be quoted.
- Spot-check individual records, including edge cases.
- Sensitivity check on the assumption most likely to change the conclusion.
- Plausibility against domain knowledge; unexplained jumps investigated.

## 5. Reproducibility
- Runs from scratch with one command; relative paths only.
- Package versions recorded (requirements / lockfile / renv); seeds set.
- Input file hash or version recorded.
- Under version control with meaningful commits.
- README: purpose, how to run, sources, definitions, assumptions, limitations.

## 6. AI-drafted code
- Every function, argument and method exists in the installed library version.
- Silent defaults: inner joins dropping rows, `dropna()`, `how=`, `errors="coerce"`, `fillna(0)`, implicit type casts.
- Error handling that hides failure: bare `except`, `pass`, broad try/except around I/O.
- "Fixes" that create internal consistency without external validation.
- Hard-coded numbers that should be computed, or computed numbers pasted as literals.
- Plausible-looking but unused or dead code paths.
- Ask for (or write) a step-by-step explanation and check it against actual outputs.

## 7. Readability
- Names say what things are.
- Comments explain why, not what.
- Consistent style (PEP 8 / a linter; tidyverse style for R).
- Files small enough to review.
