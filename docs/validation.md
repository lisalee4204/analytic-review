# Validation so far

Two blind tests, run on Nov 12, 2026, in single-agent mode. The reviewer saw only
the files under review: no answer key, no prior analysis, and no notes.

The material was a take-home analytics exercise: a census-tract extract
(547 rows, labeled ACS 2019–2023 five-year estimates) with problems built in,
and a question bound for a news story (how many children in the county live in
poverty, and which five tracts have the most). The data and the original
analysis aren't published here; a synthetic version is planned (see
[Roadmap](../README.md#roadmap)).

## Test A: does it catch the planted problems?

The reviewer got the raw extract and a deliberately naive first-pass script.
Scored against a key of issues found in a full manual analysis:

| Issue | Result |
|---|---|
| 18 duplicate rows overstating the headline count by 3.8% | Caught (exact) |
| Supplied rate column uses the wrong denominator (all children instead of children with poverty status determined), in every tract | Caught (exact); recomputed the county rate and showed 3 of the top 5 change |
| Median income coded as $0 in 11 tracts | Caught |
| 6 tracts with zero children given a 0% rate | Caught |
| Tracts with implausibly many children lacking poverty status | Partly: named 4 of 8 and explained the mechanism |
| Two tract-ID fields disagree | Caught, with the formatting pattern |
| IDs don't match real Census tracts (needed outside data) | Partly: suspected a boundary-vintage problem, labeled unverified |
| Top-5 cutoff is not a natural break; no margins of error | Caught |
| Rate-only ranking misses scale; give count and rate lists | Caught; its count list matched the manual analysis |
| No margins of error in the file | Caught |
| Median income is a weak proxy | Caught |
| Public caveats (county vs city; five-year period) | Partly: caught the period, not the county/city wording |

**9 of 11 file-only issues caught in full, 2 in part, plus a partial on the
outside-data issue. No false alarms found.**

## Test B: does it find anything in a careful analysis?

The reviewer got the full analysis pipeline (59 internal checks), the
deliverable email, and the raw data. It reran the pipeline twice (byte-identical
outputs), recomputed every quoted number with independent code (all matched),
and found issues the author had missed:

- Data-quality log entries describing work the code no longer does (left over
  from a removed step).
- An assertion that compares a value with itself and always passes.
- A concentration claim whose threshold isn't stated in the sentence.
- A public talking point that contradicts the pipeline's own logged decision.
- Wording that implies precision the estimates don't have.

**What changed after Test B.** The first run filed the author's deliberate,
disclosed judgment calls as must-fix errors. Version 0.2 added a step that reads
the author's documented decisions first and reviews them in their own section,
escalating only if a decision is wrong, unsupported, or hidden from the person
who will act on it. On a rerun, those items moved to "Deliberate decisions" or
"Should fix", and the real issues stayed.

## Limits

- Two tests on one dataset. This is a smoke test, not a benchmark.
- Single-agent mode only; team mode hasn't been scored yet.
- Run in Claude Code only; other tools not yet tested.
