# Marketing A/B Test — Ad vs PSA (Conversion Analysis)

## Overview
This project analyzes the results of a marketing experiment comparing two groups:

- **Ad group**: users who were shown an advertisement  
- **PSA group**: users who were shown a public service announcement (control)

The goal is to determine whether the ad experience led to a higher conversion rate and to explore whether ad exposure patterns (day/hour/volume) relate to conversion behavior.

This analysis was completed in Python using common experimentation and statistical testing workflows (conversion-rate comparison, significance tests, effect size, and exploratory correlations).

## Business Question
**Does showing ads (vs a PSA control) increase user conversion?**  
Secondary questions:
- Do conversions vary meaningfully based on **ad exposure volume**?
- Are there notable differences in **total ads shown across days of the week**?

## Dataset
Input file used in the notebook: `marketing_AB.csv`

Key fields:
- `test_group`: experiment group (`ad` or `psa`)
- `converted`: conversion status (boolean)
- `total_ads`: number of ads shown to a user
- `most_ads_day`: day of week with the highest ad exposure
- `most_ads_hour`: hour of day with the highest ad exposure

## Methods
### 1) Data Checks & Exploration
- Loaded and inspected the dataset (shape, dtypes, missing values)
- Confirmed two test groups were present: `ad` and `psa`
- Computed conversion rates by group

### 2) Conversion Rate Comparison (Ad vs PSA)
Tests performed:
- **Welch’s t-test** on conversion indicator (0/1) for mean difference  
  *(Note: conversion is binary, so a proportions test is often preferred; I also ran a chi-square test below.)*
- **Cohen’s d** to evaluate practical significance (effect size)
- **Chi-square test of independence** to test association between group and conversion

### 3) Exploratory Analysis
- **ANOVA**: tested whether `total_ads` differs across `most_ads_day`
- **Tukey HSD**: identified which day pairs differ in average `total_ads`
- **Pearson + Spearman correlation**: relationship between `total_ads` and `converted`

## Key Results

### Conversion Rate by Group
| Group | Conversion Rate |
|------|------------------|
| Ad   | **~2.55%** |
| PSA  | **~1.79%** |

**Absolute lift:** ~0.77 percentage points  
**Relative lift:** ~43% (relative to PSA baseline)

### Statistical Significance (Ad vs PSA)
- **Welch’s t-test:** t ≈ **8.66**, p ≈ **5.11e-18**
- **Chi-square:** χ² ≈ **54.01**, p ≈ **2.00e-13**

**Interpretation:** The difference is **statistically significant** (very small p-values), meaning it’s unlikely to be due to random chance given the sample size.

### Practical Significance (Effect Size)
- **Cohen’s d:** **~0.049**

**Interpretation:** While the result is statistically significant, the **effect size is extremely small**, suggesting the improvement may be **practically negligible** unless the business impact (incremental conversions × value per conversion) justifies the ad cost.

## Exploratory Findings

### Do total ads differ across days?
- **ANOVA on `total_ads` by day:** F ≈ **50.37**, p ≈ **2.82e-62**

**Interpretation:** Average `total_ads` differs significantly across days (this speaks to *exposure distribution*, not necessarily conversion).

- **Tukey HSD:** indicates multiple day-pair differences are statistically significant (Friday shows large separation vs several other days).

### Does ad volume relate to conversion?
- **Pearson correlation (`total_ads` vs `converted`):** r ≈ **0.217**
- **Spearman correlation:** ρ ≈ **0.193**
- Both p-values ≈ 0 (highly significant due to large sample)

**Interpretation:** There is a **weak positive relationship** between higher ad exposure and conversion. Statistically detectable, but not strong.

## Conclusion
1. **Ads outperform PSA on conversion rate** (~2.55% vs ~1.79%).  
2. The difference is **statistically significant**, supported by both a t-test and chi-square test.
3. The **effect size is very small (Cohen’s d ~0.049)**, meaning the real-world value depends on unit economics:
   - If each conversion is highly valuable and ad costs are low, this lift could be worth it.
   - If margins are tight or ads are expensive, the gain may not justify scaling.

## Recommendations / Next Steps
To make the result more decision-ready:
1. **Run a proper two-proportion z-test** (or logistic regression) as a primary inference method for binary conversion.
2. Report **confidence intervals** for the conversion lift (absolute and relative).
3. Segment the lift by:
   - `most_ads_day`, `most_ads_hour`, and/or ad exposure bands (`total_ads` buckets)
4. Add a simple cost model:
   - incremental conversions × value per conversion − cost of ads  
   This translates statistical output into business impact.

## How to Run
1. Clone the repo
2. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib scipy statsmodels scikit-learn
