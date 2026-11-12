# analytic-review

*Most AI review tools check whether code runs. This one checks whether the numbers are right.*

An agent skill that reviews a data analysis end to end, the way a careful senior
analyst would before the numbers go to a decision-maker or the public:

1. **Question first.** What decision does this serve, and who will read it?
2. **Method fit.** Does the design answer the question? Are its assumptions met?
3. **Code and pipeline.** Joins, filters, denominators, units, geography, with the checks actually run.
4. **Results.** Totals reconciled; quoted numbers recomputed independently; a sensitivity check.
5. **Reproducibility.** A clean rerun from scratch.
6. **AI-drafted code.** Hallucinated functions, silent defaults, hidden failures, and "fixes" that are only internally consistent.
7. **What can be said.** The caveats a non-technical reader must carry.

It separates **errors** from the author's **deliberate, disclosed decisions**, and
reviews those on their own terms instead of flagging every judgment call.

## Team mode

For a substantial analysis, the skill splits the review across eight reviewer
agents that run in parallel, then sends every serious finding to an independent
verifier that tries to disprove it:

| Reviewer | Looks for |
|---|---|
| Method fit | Wrong design for the question; violated assumptions |
| Code and pipeline | Silent data loss, join explosions, wrong denominators |
| Significance and uncertainty | Missing intervals, rankings inside the error, multiple comparisons |
| Generalizability | Who's missing, time period, ecological inference |
| Privacy and disclosure | Identifiers, re-identification risk, small cells, credentials |
| Domain and literature | Known drivers of the outcome, measurement issues (cited) |
| Common pitfalls | Simpson's paradox, survivorship bias, average of averages, and more |
| Reproducibility and AI code | Clean rerun, versions, AI-drafted code checks |

Without a subagent tool, the same eight passes run in sequence. Small jobs use
three or four combined reviewers.

## Output

One report: a verdict (**Ready to share / Share with caveats / Needs revision**),
then must-fix items (each with location, consequence, fix, and whether the
reviewer reproduced it), the author's deliberate decisions assessed, should-fix
items, checks run, reproducibility, AI-code issues, and the caveats the reader
must carry.

## Install

The skill is a folder in the open [Agent Skills](https://agentskills.io) format
(`SKILL.md` plus reference files), so it works in tools that read SKILL.md skills.

- **Claude Code:** copy `skills/analytic-review` to `~/.claude/skills/` (all
  projects) or `.claude/skills/` in a project.
- **Claude.ai:** zip the `analytic-review` folder and upload it under
  Settings → Capabilities → Skills.
- **Other tools (Codex, Cursor, Gemini CLI, GitHub Copilot):** copy the folder
  into the directory your tool reads skills from. Team mode needs subagent
  support; otherwise the skill runs sequentially.

Then ask, for example: *"Review analysis.py and report.md before this goes out"*
or *"Is this the right test for this data?"*

## Validation

Early results from two blind tests on a census-tract dataset with planted
problems: the skill caught **9 of 11** planted issues in full and 2 in part,
with no false alarms found, and found real issues a careful manual analysis had
missed. Details and limits: [docs/validation.md](docs/validation.md).

## Roadmap

- [ ] Synthetic benchmark: small datasets with planted errors (duplicates,
      wrong denominators, join explosion, Simpson's paradox, small cells,
      causal overreach, AI "fixes"), scored for catch rate and false alarms
- [ ] Score team mode against single-agent mode
- [ ] Cross-tool runs (Claude Code, Codex, Cursor, Gemini CLI)
- [ ] Optional blind replication by a second model for headline numbers
- [ ] Packaging as a Claude Code plugin

## Contributing

Issues and pull requests are welcome, especially planted-error test cases from
real review experience. Please don't submit real person-level or restricted data.

## License

[Apache License 2.0](LICENSE). See [NOTICE](NOTICE) for credits.
