# AI Trust Gap Detector — AI-Validated Business Intelligence Dashboard

An analytics project that generates AI-powered business insights from real sales data, then automatically checks how much those insights should actually be trusted — catching cases where an AI model sounds confident but the underlying data says otherwise.

## The Problem

AI tools like ChatGPT and Claude generate business insights and forecasts in a consistently confident tone, regardless of whether that confidence is actually earned by the data behind it. A claim based on 6,000 data points and a claim based on 60 data points can sound equally certain — and that mismatch between **tone** and **actual reliability** is a real risk for anyone acting on AI-generated advice without checking.

This project builds a "Trust Gap Detector": a tool that scores every AI-generated insight against the real data (sample size + volatility) and flags when the AI's confidence doesn't match the data's reliability.

## What This Project Does

1. **Cleans and validates** real retail transaction data (Kaggle Superstore dataset, 9,994 orders, 2014–2017)
2. **Calculates KPIs** — margin, margin volatility (standard deviation), and sample size across categories, regions, and sub-categories
3. **Generates AI insights** using Llama 3.3 (via the Groq API) based on those KPIs
4. **Scores each AI claim** using a custom Trust Gap algorithm that combines two signals:
   - **Volatility** — how much does this category's margin swing around? (unstable = lower trust)
   - **Sample size** — how much data actually backs this claim? (small sample = lower trust)
5. **Visualizes everything** in an interactive Power BI dashboard

## Key Findings

- **The AI got it backwards on Technology.** It described Technology as needing "careful management" due to volatility concerns — but the data shows Technology actually has the *lowest* margin volatility (std = 0.231) of all three categories, making it the most stable and predictable.
- **The AI overstated confidence in Office Supplies.** It confidently predicted continued growth — but Office Supplies has the *highest* margin volatility (std = 0.548) of the three categories, meaning its profitability is the least predictable.
- **A real business problem, found independently of the AI:** the Central region has a **negative average profit margin (-10.4%)**, while every other region is solidly profitable (16–22%). This finding came directly from KPI analysis, not from the AI.
- **Sample size alone is misleading.** Copiers (68 orders) turned out to be low-volatility and stable, while Binders (1,522 orders) turned out to be highly volatile despite the large sample. This is why the Trust Gap Detector combines *two* signals instead of relying on sample size alone.
- **Data quality check:** found and removed 8 duplicate transactions (same Order ID + Product ID logged twice) that a basic row-level duplicate check would have missed.

## How the Trust Gap Score Works

Each category/sub-category gets scored on two signals:

| Signal | Low Trust | Medium Trust | High Trust |
|---|---|---|---|
| Margin volatility (std dev) | ≥ 0.45 | 0.28 – 0.45 | < 0.28 |
| Sample size (n orders) | < 150 | 150 – 500 | ≥ 500 |

The two scores are combined into an overall confidence label (Low / Medium / High) with a plain-English explanation, so the output is usable by a non-technical stakeholder, not just a data scientist.

## Tech Stack

- **Python** (pandas, numpy) — data cleaning and KPI calculation
- **Groq API** (Llama 3.3 70B) — AI insight generation
- **Power BI** — dashboard and visualization
- **Google Colab** — development environment

## Project Structure

```
ai-trust-gap-detector/
├── data/
│   ├── raw_superstore.csv          # Original Kaggle Superstore dataset
│   └── clean_superstore.csv        # Cleaned, validated dataset
├── notebooks/
│   └── trust_gap_analysis.ipynb    # Full analysis: cleaning, KPIs, AI calls, scoring logic
├── dashboard/
│   ├── AI_Validated_BI_Dashboard.pbix
│   └── dashboard_screenshot.png
└── README.md
```

## Dataset

[Sample Superstore dataset](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final) — publicly available on Kaggle, 9,994 real retail transactions (2014–2017).

## Author

Chandra Bose Boyapati — [LinkedIn](https://linkedin.com)
