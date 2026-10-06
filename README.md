# Marketing A/B Test: Hypothesis Testing and Heterogeneity Analysis

Statistical analysis of a marketing A/B test with 588,101 users, measuring whether exposure to an ad increases conversion compared with a public service announcement (PSA) control, and whether the effect depends on the day of the week or the hour of the day.

## Business question

1. Does the ad increase conversion over the PSA control?
2. By how much, and how certain is that estimate?
3. Does the ad's effect depend on *when* users see most of their ads (day of week, hour of day)?

## Data

`marketing_AB.csv`: one row per user.

| Column | Description |
|---|---|
| `test group` | `ad` (treatment, 564,577 users) or `psa` (control, 23,524 users) |
| `converted` | Whether the user converted (True/False, cast to 0/1) |
| `total ads` | Number of ads the user saw |
| `most ads day` | Day of the week with the most ad exposure |
| `most ads hour` | Hour of the day with the most ad exposure |

The groups are heavily imbalanced (about 96% of users are in the ad group).

## Methods

- **Overall effect:** two-proportion z-test (pooled SE for the test, unpooled SE for the 95% CI on the difference), with absolute and relative lift.
- **Day and hour effects on conversion:** chi-square tests of independence, with Cramér's V as the effect size.
- **Pairwise day comparisons:** two-proportion z-tests with a Bonferroni correction (21 comparisons, alpha ≈ 0.0024).
- **Does the ad effect vary by day or hour?** Per-segment lift (ad minus PSA) with 95% confidence intervals, then Cochran's Q and I² to test heterogeneity of the lift across segments.
- **Small-sample handling:** Agresti-Caffo adjusted intervals (add one success and one failure per group) to avoid degenerate standard errors where the PSA group had zero conversions.

A t-test and ANOVA were not used because the outcome is binary; proportion and chi-square tests are the appropriate choice.

## Key findings

| Result | Value |
|---|---|
| Conversion, ad group | 2.55% |
| Conversion, PSA group | 1.79% |
| Absolute lift | 0.77 pp (95% CI: 0.60 to 0.94 pp) |
| Relative lift | about 43% |
| Significance | z = 7.37, p < 0.001 |

- **Day of week:** conversion differs by day (chi-square = 410.05, dof = 6, p < 0.001), but the effect is small (Cramér's V = 0.026). Conversion peaks on Monday (3.28%) and Tuesday (2.98%) and is lowest on Saturday (2.11%).
- **Ad lift varies by day** (Cochran's Q test, p < 0.001, I² ≈ 76%). Lift is largest on Tuesday (1.60 pp) and smallest on Thursday (0.14 pp, CI includes zero).
- **Hour of day:** conversion differs by hour (chi-square = 430.8, dof = 23, p < 0.001, Cramér's V = 0.027), rising from late morning into the evening.
- **Ad lift is stable across hours** (Q = 15.12, dof = 23, p = 0.89, I² = 0%; pooled lift about 0.74 pp).

## Caveats

- **Observational splits.** Day and hour are not randomly assigned, so these are associations, not causal effects. The overall ad vs PSA comparison is causal only if assignment to groups was random.
- **Control is a PSA, not "no ad".** The result measures ad vs PSA.
- **Small control group.** Day- and hour-level estimates for the PSA group are imprecise, especially overnight (hours 0 to 7), where lift was not interpreted.
- **No multiple-comparison correction on the per-segment confidence intervals.** The pairwise day tests are Bonferroni-corrected; the lift CIs are not.
- **Large sample.** With 588K users, almost any difference is statistically significant, so effect sizes and confidence intervals matter more than p-values.

## Repository structure

```
.
├── hypothesis-testing-a-b-testing.ipynb
├── marketing_AB.csv
├── requirements.txt
└── README.md
```

## Reproduce

```bash
pip install pandas numpy scipy matplotlib seaborn
jupyter notebook hypothesis-testing-a-b-testing.ipynb
```

Place `marketing_AB.csv` in the same folder as the notebook.

## Tools

Python, pandas, NumPy, SciPy, Matplotlib, Seaborn
