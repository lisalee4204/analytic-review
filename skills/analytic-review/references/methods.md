# Method fit

## Six framing questions
1. **Decision:** describe, compare, predict, or estimate an effect?
2. **Outcome type:** continuous, ordinal or skewed, categorical, count, rate, time to event?
3. **Predictors:** how many, which types?
4. **Independence:** paired, repeated, clustered (people in places, places in regions), or spatially linked?
5. **Sample or population:** survey data (e.g. ACS) carries margins of error; administrative data may be a near-census with its own coverage gaps.
6. **Causal?** If yes, what stands in for the counterfactual?

## Selection table (adapted from UCLA OARC's structure)

| Design | Continuous outcome | Ordinal / skewed | Categorical |
|---|---|---|---|
| One variable vs a known value | One-sample t-test | One-sample Wilcoxon signed-rank | Binomial; chi-square goodness of fit |
| Two independent groups | Independent t-test | Mann-Whitney U | Chi-square; Fisher's exact (small cells) |
| 3+ independent groups | One-way ANOVA | Kruskal-Wallis | Chi-square |
| Two paired measures | Paired t-test | Wilcoxon signed-rank | McNemar |
| 3+ repeated measures | Repeated-measures ANOVA; mixed model | Friedman | GEE / mixed-effects logistic |
| 2+ categorical predictors | Factorial ANOVA | Ordered logistic | Factorial logistic; log-linear |
| One continuous predictor | Correlation; simple regression | Spearman | Simple logistic |
| Several mixed predictors | Multiple regression; ANCOVA | Ordered logistic | Multiple / multinomial logistic |
| Several outcomes | MANOVA; multivariate regression | Rank-based or transform | Multivariate / multinomial models |

## Additions common in applied social-policy analytics
- **Counts:** Poisson; negative binomial when variance exceeds the mean.
- **Rates across areas of different sizes:** count model with a population offset; consider empirical Bayes smoothing for small areas.
- **Nested data:** multilevel / mixed-effects models or cluster-robust standard errors.
- **Small-area survey estimates:** propagate margins of error; test differences before ranking; flag estimates with a high coefficient of variation.
- **Spatial:** Moran's I for clustering; Getis-Ord hot spots; spatial lag or error models.
- **Program evaluation without randomization:** difference-in-differences (check parallel pre-trends), regression discontinuity (eligibility cutoffs), interrupted time series (policy start dates), matching / propensity scores (state the overlap and balance).
- **Time to event:** Kaplan-Meier; Cox proportional hazards (check the assumption).
- **Descriptive questions** often need no model. Ranking, rates vs counts, and clear denominators may be the whole job; don't add a model to look rigorous.

## Red flags in method choice
- Causal language from a cross-sectional correlation.
- Averages of rates across units of very different sizes (average of averages).
- Parametric tests on heavily skewed small samples without a check.
- Many comparisons with no adjustment, or the "garden of forking paths".
- Strict rank order of small-area estimates whose intervals overlap.
- An ecological inference (area-level relationship read as individual-level).
