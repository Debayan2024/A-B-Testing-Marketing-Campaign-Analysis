# Marketing Campaign Analysis — Did the Final Campaign Actually Work Better?

## TL;DR
The Final campaign converted 14.9% of customers vs. 6.4% for Campaign A — a statistically significant
lift (χ² = 82.93, p < 0.001). But **conversion rate alone doesn't justify a budget reallocation**: this
analysis also checks sample sizes, effect size, and whether "Final" is really a fair comparison against
"first," and flags what would need to change before this goes into a client deck.

## Business Question
Marketing ran six campaigns sequentially. Leadership wants to know: is the Final campaign meaningfully
better than the first, or is the gap noise / an artifact of how the campaigns were sequenced (e.g., Final
was only sent to customers who survived / didn't churn out of earlier campaigns)?

## Data
- Source: [Kaggle – Marketing Campaign Dataset](https://www.kaggle.com/datasets/rodsaldanha/arketing-campaign), 2,240 customers
- Target columns: `AcceptedCmp1`–`AcceptedCmp5` (binary, campaigns A–E), `Response` (binary, Final campaign)
- **Data quality check performed:** nulls checked per column (`Income` had missing values, `Year_Birth` had
  outliers implying implausible ages) — see `01_eda.ipynb`. These were profiled but not yet imputed; if used
  for segmentation below, imputation strategy is documented before modeling.
- **Known limitation:** the dataset does not indicate whether a customer who saw Campaign A was excluded
  from later campaigns after converting, or whether "Final" respondents overlap with earlier accepters. This
  matters — if Final was targeted at a pre-filtered, warmer audience, some of the lift is selection, not
  campaign quality. Flagged here rather than silently assumed away.

## Methodology
1. **EDA:** conversion rate per campaign, class balance, missingness (`01_eda.ipynb`)
2. **Hypothesis test:** two-proportion comparison, Campaign A vs. Final
   - H₀: conversion rate(Final) = conversion rate(A)
   - H₁: conversion rate(Final) ≠ conversion rate(A)
   - Test: chi-square test of independence on the 2×2 contingency table (converted/not × campaign)
   - **Assumption check:** all expected cell counts > 5 → chi-square is valid here (verified, not assumed)
3. **Effect size, not just p-value:** p < 0.05 tells you the difference is unlikely to be chance — it says
   nothing about whether the difference is *big enough to act on*. Cohen's h and a 95% CI on the rate
   difference are reported alongside the p-value for this reason.
4. **Multiple comparisons:** five campaigns (A–E) exist besides Final. If the "Campaign A vs Final" pair was
   chosen after eyeballing the bar chart, that's a form of multiple testing — the p-value should be read
   accordingly (or the comparison should be pre-registered / a correction applied if comparing several pairs).

## Results

| Campaign | n (accepted) | Conversion Rate | 
|----------|-------------:|-----------------:|
| A (Cmp1) | 144 | 6.4% |
| B (Cmp2) | 30  | 1.3% |
| C (Cmp3) | 163 | 7.3% |
| D (Cmp4) | 167 | 7.5% |
| E (Cmp5) | 163 | 7.3% |
| Final    | 334 | 14.9% |

**Campaign A vs. Final:** χ² = 82.93, p = 8.5e-20, effect size (Cohen's h) = [to be reported], 95% CI on the
rate difference = [to be reported]. Reject H₀ — the difference is very unlikely to be random noise.

![Conversion Rates Bar Chart](results/conversion_rates.png)
![Hypothesis Testing Flowchart](results/hypothesis_testing_flow.png)

## What This Does and Doesn't Tell Leadership

**Supported by the data:**
- Final's conversion rate is significantly and substantially higher than Campaign A's — this is not sampling noise.
- Campaign B underperformed every other campaign by a wide margin and is worth a targeting review on its own.

**Not yet supported — needs more analysis before it goes in a client recommendation:**
- *Causal attribution.* "Final worked because of better messaging/targeting" is a hypothesis, not a finding.
  Without knowing audience composition per campaign, the lift could be selection effects, timing, seasonality,
  or audience warmth rather than creative/targeting quality.
- *Segmentation.* Did the lift hold across income bands, tenure, and channel, or is it concentrated in one
  segment? A recommendation to "scale Final's approach" is much stronger if it holds across segments.
- *ROI, not just conversion rate.* Conversion rate up doesn't automatically mean ROI up if Final targeted a
  smaller, cheaper-to-reach, or higher-cost list. Budget reallocation calls need a cost/revenue-per-conversion
  view, not just a rate.

## Recommendation (framed appropriately)
The data supports **investigating** what changed operationally between Campaign A and Final (audience
selection criteria, offer, channel, timing) before recommending a budget shift. It does not yet, on its own,
support a reallocation decision — that claim overstates what a single two-proportion test can show.

## Tools
- Python: Pandas, NumPy, SciPy (`stats.chi2_contingency`, effect size calc), Statsmodels (CI on proportions)
- Seaborn/Matplotlib for visualization
- SQL: [if any extraction/aggregation was done in SQL rather than pandas, document the query here — the JD
  asks specifically for SQL alongside Python]

## Repo Structure
```
├── data/                     # raw + cleaned data (not committed if proprietary)
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_hypothesis_test.ipynb
│   └── 03_effect_size_and_caveats.ipynb
├── results/
│   ├── conversion_rates.png
│   └── hypothesis_testing_flow.png
├── src/                      # reusable functions (e.g., a `two_proportion_test()` helper, not copy-pasted per notebook)
└── README.md
```

## Notes to Reviewer
This project is a good sandbox for two-proportion hypothesis testing. What would move it from "notebook
exercise" to "consulting-ready deliverable" — the level this role operates at day-to-day — is: (1) stating
the causal-inference limits explicitly instead of implying campaign quality caused the lift, (2) reporting
effect size and confidence intervals alongside the p-value, (3) showing the SQL/extraction step rather than
starting from a clean CSV, and (4) separating "statistically significant" from "actionable business
recommendation" in the writeup — the two are not the same claim.
