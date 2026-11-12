# Reviewer briefs (team mode)

Each reviewer gets the shared brief plus its section below. Every finding uses
the same shape: **location · problem · consequence · fix · severity
(must / should / note) · evidence (reproduced / inferred / cited)**. Report
nothing you can't point to.

Every reviewer also receives the author's documented decisions (from step 0).
Before filing a finding, check whether it is a deliberate, disclosed choice;
if so, file it under "Deliberate decisions" with your assessment instead of as
an error, unless it meets the escalation test in SKILL.md step 0.

## 1. Method fit
Does the design answer the stated question? Use `methods.md`: framing
questions, selection table, red flags. Check assumptions the method depends on
(independence, distribution, parallel trends, overlap). Recommend a different
method only if it changes the conclusion or its confidence.

## 2. Code and pipeline
Checklist sections 1–3. Execute checks where possible: row counts at each
stage, key uniqueness, join cardinality, rows dropped by each filter,
denominator universes, units and geography. Flag silent data loss first.

## 3. Significance and uncertainty
Are effects reported with size and uncertainty, not just p-values? Check:
confidence intervals or margins of error carried through derived numbers;
multiple comparisons; statistical vs practical significance; power or sample
size for null results; whether differences used for rankings exceed their
uncertainty; survey weights and design effects where relevant.

## 4. Generalizability
To whom, where and when do the results apply? Check: the population and
sampling frame vs the population the conclusion names; time period and data
vintage; geography and boundary changes; selection into the data (who is
missing: non-respondents, people outside administrative systems); ecological
inference (area-level patterns read as individual-level); transfer to other
places or programs.

## 5. Privacy and disclosure
Check: direct identifiers (names, addresses, IDs, dates of birth) in code,
outputs, logs or committed files; quasi-identifiers that combine to
re-identify (small geography + age + race + rare condition); small cells in
published tables (apply the data provider's suppression rule; a common
threshold is fewer than 10–11 people); secrets or credentials in code;
data-use agreement limits on sharing or linking; whether outputs leave the
approved environment. Flag, don't print, any sensitive value you find.

## 6. Domain and literature
What does research and current practice say about this outcome? Search for
and cite (peer-reviewed work, federal statistical agencies, established
research centers): the factors known to drive the outcome, and whether they
are included, controlled for, or acknowledged; known measurement issues with
these data (e.g. ACS small-area reliability, administrative undercount);
standard definitions the analysis should match; whether the findings agree
with prior evidence, and if not, a plausible explanation. Label anything not
backed by a source as the reviewer's judgment.

## 7. Common pitfalls
Sweep for: join explosion; denominator shifting; average of averages;
Simpson's paradox; survivorship and selection bias; regression to the mean;
incomplete-period comparisons; double counting across overlapping groups;
mixing counts and rates; ranking noisy small-area estimates; causal language
from correlation; base-rate neglect; cherry-picked time windows.

## 8. Reproducibility and AI-drafted code
Checklist sections 5–7. Attempt a clean rerun and compare outputs. Apply the
AI-drafted-code checks, with special attention to "fixes" that create internal
consistency without external validation, and to numbers pasted as literals.

## Verifier
Given one finding, the author's documented decisions, and access to the same
files: try to disprove it, including by checking whether it was a deliberate,
disclosed decision or is consistent within the author's own framing. Rerun the
check, read the code path, open the cited source. Return **verified**,
**refuted** (with reason), or **unclear**. Never verify your own finding.
