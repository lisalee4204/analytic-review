---
name: analytic-review
description: "Review an analysis end to end: whether the method fits the question, whether the code and data pipeline are correct, whether results reproduce, and whether AI-drafted code was verified. Use when asked to review, audit, QA or check an analysis script, notebook, SQL plus results, or a finished analysis before it is shared; also for 'is this the right test/method', 'check my code before I send this', or 'did the AI get this right'. Not for general software PR review."
---

# Analytic Review

One systematic pass over an analysis: **method fit → code and data pipeline →
results → reproducibility → AI-drafted code → what can be said publicly.**

Existing skills cover pieces of this (results QA, software code review, method
selection); none covers the whole pass. This skill fills that gap. If
Anthropic's Data plugin is enabled, also run its `validate-data` skill for the
results and pitfall checks and fold its findings into this report rather than
duplicating them.

## Stance

- **Run things; don't only read them.** Where the code and data are available,
  execute the checks (row counts, key uniqueness, join cardinality,
  reconciliations, a rerun from scratch). A finding you reproduced outranks one
  you inferred.
- **Verify against something outside the code.** Internal consistency is not
  validation. A "fix" that makes two fields agree is a hypothesis until an
  external source confirms it.
- **Separate what the data supports from what the reader wants it to say.**
- **Be specific.** Every finding names the file and line (or cell), the
  concrete failure ("an inner join drops 214 tracts with no income record"), and
  the fix.
- **Respect the author.** Review the work, not the person. Don't pad with praise
  or soften real problems.

## Workflow

### 0. Get the question and the author's decisions first
Find, or ask for, the decision or question the analysis serves and the
audience for its output. Review everything against that. If none is stated,
infer it and state your inference at the top of the report.

Then collect the author's **documented decisions**: evaluator or methods
notes, a README, a decision or data-quality log, code comments, caveats in the
deliverable. A choice the author made knowingly, with the trade-off stated, is
a **deliberate decision**, not an error. Review it as one (see Output): say
whether the reasoning holds, what the alternative would cost, and what would
change your view. Escalate it to must-fix only if it is factually wrong,
unsupported by the data, or hidden from the reader who will act on it. A
decision disclosed to a reviewer but not to the end reader is a disclosure
gap: report the gap, not the decision.

Before calling something inconsistent, check whether it is consistent within
the author's own framing (e.g. a count quoted on the same denominator as the
rate it supports is consistent, even if another total also exists).

### 1. Map the pipeline
List the stages as they actually exist: load → validate → clean → transform →
analyze → output. Note inputs (with versions or hashes where possible),
settings, and outputs. Flag missing stages (e.g. no validation step).

### 2. Method fit
Use `references/methods.md`. Answer the six framing questions (decision,
outcome type, predictors, independence, sample vs population, causal or not),
then check the chosen method against the selection table and the
domain additions (counts and rates, nested data, small-area estimates, spatial,
quasi-experimental designs). Name a better method only if it would change the
conclusion or its confidence, and say why.

### 3. Code and data pipeline
Use `references/checklist.md` sections 1–3 (structure, data in, transformations).
Execute the checks where possible. Pay most attention to joins, filters,
denominators, units and geography: that's where most analysis errors are.

### 4. Results
Checklist section 4. Reconcile totals to the source or an independent figure;
recompute two or three quoted numbers independently; run a sensitivity check
on the assumption most likely to move the conclusion.

### 5. Reproducibility
Checklist section 5. Try a clean rerun if the environment allows; report
whether outputs match.

### 6. AI-drafted code
Checklist section 6. Apply it whenever code may have been AI-drafted (assume
it may have been unless told otherwise).

### 7. What can be said
Draft the caveats a non-technical reader must carry: data vintage and
geography, uncertainty (margins of error), definitions, and what the analysis
cannot show (causation, small-area rank order). Flag any claim in the
narrative that the results don't support.

## Team mode (when subagents are available)

For a substantial analysis, or when the user asks for a thorough or team
review, split the pass across reviewer agents that run in parallel, then verify
before reporting. If no agent or subagent tool is available, run the same
dimensions yourself in sequence.

1. **Brief once.** Write a shared brief: the question, the audience, the file
   paths, the pipeline map from step 1, and the verdict scale. Every reviewer
   gets the same brief plus its own section from `references/reviewers.md`.
2. **Fan out** to the reviewers in `references/reviewers.md`: method fit;
   code and pipeline; significance and uncertainty; generalizability; privacy
   and disclosure; domain and literature; common pitfalls; reproducibility and
   AI-drafted code. Skip a reviewer only when its dimension can't apply (e.g. no
   person-level data → privacy reviewer checks only small-cell disclosure).
   Each returns findings in the shared format: location, problem, consequence,
   fix, severity, evidence (reproduced / inferred / cited).
3. **Verify.** Send every must-fix and should-fix finding to an independent
   verifier agent that did not produce it, with the instruction to try to
   disprove it (rerun the check, read the code path, check the source). Drop
   findings that don't survive; mark the rest "verified".
4. **Merge.** Deduplicate across reviewers, keep the most specific version of
   each finding, and write one report in the format below. Domain and
   literature findings must cite their sources.

Keep the team proportional: a one-page script doesn't need eight agents. Use
three or four combined reviewers for small jobs.

## Output

```
## Analytic Review: <name>

**Question it serves:** <stated or inferred>
**Verdict:** Ready to share | Share with caveats | Needs revision
**Confidence:** High | Medium | Low, and why in one line

### Must fix (would change a number or a conclusion)
1. <file:line> <what's wrong> → <consequence> → <fix>  [reproduced | inferred]

### Deliberate decisions (disclosed by the author)
1. <decision> · <author's stated reason> · <does the reasoning hold?> ·
   <cost of the alternative> · <what would change this view>

### Should fix (risk, fragility, or clarity)
1. ...

### Method fit
<chosen method; fits / doesn't; better option only if it changes the answer>

### Checks run
- <check>: <result>   (e.g. "Rows in vs. out of join: 529 → 529, one-to-one ✔")

### Reproducibility
<clean rerun result; what's missing (versions, seeds, paths, hash, README)>

### AI-drafted code
<issues found, or "none found"; note anything only internally consistent>

### Significance, generalizability, privacy, domain (team mode or when relevant)
<one short paragraph each; omit any that don't apply>

### Caveats the reader must carry
- ...
```

Keep the report to what matters. Lead with the must-fix items. Never list a
deliberate, disclosed decision under must-fix unless it meets the escalation
test in step 0.

In single-agent mode there is no separate verifier, so before writing the
report, re-check each must-fix finding yourself by trying to disprove it
(rerun the check, reread the code path, look for the author's rationale).
Drop or downgrade anything that doesn't survive. Don't list
passing checks individually unless asked; summarize them under "Checks run".

## Credits

Results and pitfall checks draw on Anthropic's `validate-data` skill
(anthropics/knowledge-work-plugins, Apache-2.0). Method selection follows the
structure of UCLA OARC's "What statistical analysis should I use?". Code checks
draw on Google's engineering-practices code review guide; Wilson et al., "Good
enough practices in scientific computing" (PLOS Comp Bio, 2017); The Turing
Way; Cookiecutter Data Science; and PEP 8. Wording here is original.
