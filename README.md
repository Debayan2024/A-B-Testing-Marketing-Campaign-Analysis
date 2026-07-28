# Did the Final Campaign Actually Work Better?

## Quick summary
Short answer: yes, the Final campaign converted a lot more people than Campaign A — 14.9% vs 6.4%, and
that gap is way too big to be random chance (χ² = 82.93, p < 0.001). Longer answer: before anyone uses this
to move budget around, there are a few things worth checking first, and I've tried to be upfront about them
below instead of just presenting the p-value and calling it a day.

## The question
Marketing ran six campaigns back to back. Leadership wants to know if the last one (Final) genuinely
performed better than the first one (Campaign A), or if that difference is just noise — or worse, an
artifact of how the campaigns were rolled out (e.g. if Final only went out to people who hadn't already
converted or dropped off earlier).

## About the data
Working with the [Kaggle Marketing Campaign dataset](https://www.kaggle.com/datasets/rodsaldanha/arketing-campaign)
— about 2,240 customers. The columns I care about are `AcceptedCmp1` through `AcceptedCmp5` (campaigns A–E)
and `Response`, which is the Final campaign.

Before doing anything else I checked for nulls and obvious garbage — `Income` has some missing values,
and `Year_Birth` has a few entries that would put someone at like 120+ years old, which is clearly wrong.
I've profiled these in `01_eda.ipynb` but haven't imputed anything yet, since I'm not using those columns
in the core test below. If they come into play later (say, for segmentation), I'll deal with imputation
then rather than guessing now.

One thing I want to flag honestly: the dataset doesn't tell you whether someone who converted on Campaign A
was even eligible to receive later campaigns, or whether the people who saw "Final" overlap with people who
already said yes earlier. If Final was sent to a smaller, already-warmer group, some of that 14.9% is just
selection bias, not the campaign itself being better. I don't have a way to fully rule this out with what's
in the dataset, so I'm naming it here rather than pretending the comparison is cleaner than it is.

## How I approached it
1. **EDA first** — conversion rate per campaign, checked class balance, checked missingness (`01_eda.ipynb`).
2. **Then the actual test** — comparing conversion rates between Campaign A and Final:
   - H₀: they convert at the same rate
   - H₁: they don't
   - Used a chi-square test of independence on the 2x2 table (converted / not, by campaign)
   - Checked the assumption before trusting the test — all expected cell counts were above 5, so chi-square
     is actually appropriate here, not just assumed to be.
3. **Effect size, because p-values alone don't tell the whole story** — p < 0.05 just means "probably not
   random." It doesn't tell you if the difference is actually big enough to act on. So I'm also reporting
   Cohen's h and a 95% confidence interval on the rate difference alongside the p-value.
4. **Being honest about multiple comparisons** — there are five campaigns besides Final. If I picked
   "A vs Final" after looking at the bar chart and noticing it was the biggest gap, that's technically
   fishing, and the p-value should be read with that in mind. Worth a proper correction if I end up
   comparing more pairs later.

## Results

| Campaign | n (accepted) | Conversion Rate |
|----------|-------------:|-----------------:|
| A (Cmp1) | 144 | 6.4% |
| B (Cmp2) | 30  | 1.3% |
| C (Cmp3) | 163 | 7.3% |
| D (Cmp4) | 167 | 7.5% |
| E (Cmp5) | 163 | 7.3% |
| Final    | 334 | 14.9% |

**Campaign A vs. Final:** χ² = 82.93, p = 8.5e-20, Cohen's h = [TODO], 95% CI on the rate difference = [TODO].
Rejecting H₀ here — this difference is not something I'd chalk up to sampling noise.

<img width="1027" height="725" alt="conversion_rates" src="https://github.com/user-attachments/assets/8f607fff-9753-4a22-ad52-44fae9cb7872" />

<img width="1589" height="964" alt="hypothesis_testing_flow" src="https://github.com/user-attachments/assets/864c03ed-b8a2-4c17-9e8e-d8289a2a76bc" />


## What I think this actually tells us

**Pretty confident about:**
- Final really did convert more people than Campaign A, and it's not a small effect.
- Campaign B is clearly the weak link here — 1.3% is well below everything else, and that alone seems
  worth a targeting review on its own, separate from the A-vs-Final question.

**Not confident about yet — and I don't think this analysis should be used to claim otherwise:**
- *Why it worked.* It's tempting to say "better messaging" or "better targeting" caused the lift, but
  that's a guess, not something the data shows. Without knowing who each campaign was sent to, the gap
  could just as easily be timing, seasonality, or a warmer audience.
- *Whether it holds up across segments.* Does the lift show up across income levels, tenure, channel? If
  it's concentrated in one slice of customers, "scale what Final did" is a much weaker recommendation than
  if it holds everywhere.
- *ROI vs conversion rate.* These aren't the same thing. If Final's list was smaller or more expensive to
  reach, a higher conversion rate doesn't automatically mean it made more money. Any budget conversation
  needs cost-per-conversion or revenue-per-conversion, not just this percentage.

## So what would I actually recommend?
Based on what's here, I'd say it's worth digging into *what changed* between Campaign A and Final —
targeting criteria, offer, channel, timing — before anyone commits to shifting budget. I wouldn't take this
one test as justification for a reallocation decision on its own; that's a bigger claim than a single
two-proportion test can support, and I'd rather say that plainly than oversell the result.

## Tools used
- Python: Pandas, NumPy, SciPy (`chi2_contingency`, effect size calc), Statsmodels for the CI on proportions
- Seaborn / Matplotlib for the charts
- SQL: [add the extraction query here if any part of the pull/aggregation happened in SQL rather than pandas]

## Repo structure
```
├── data/                     # raw + cleaned data (not committed if proprietary)
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_hypothesis_test.ipynb
│   └── 03_effect_size_and_caveats.ipynb
├── results/
│   ├── conversion_rates.png
│   └── hypothesis_testing_flow.png
├── src/                      # reusable helpers, e.g. a two_proportion_test() function instead of copy-pasting the test logic per notebook
└── README.md
```

## A few honest notes to whoever's reviewing this
This started as a simple two-proportion test and I think it's a decent one. What I tried to add on top,
compared to a more surface-level version of this project, is: being upfront about what could be causing
the lift besides "the campaign was better," reporting effect size and confidence intervals instead of just
the p-value, and being careful not to blur "statistically significant" into "we should act on this now" —
those are two different claims and I don't want to accidentally conflate them.
