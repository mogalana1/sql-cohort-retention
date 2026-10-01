## Business Question
Which customer cohorts retain best over time? Which segments (by recency, frequency, monetary value) drive the most revenue?

## What This Analysis Does
- Groups customers by signup/purchase month (cohort assignment)
- Tracks % of customers returning in months 1, 2, 3, 6, 12
- Applies RFM segmentation (Recency, Frequency, Monetary) using NTILE()
- Identifies "whale" segments vs. churned segments

## Make-or-Break Techniques
- `MIN(order_date) OVER (PARTITION BY customer_id)` for cohort assignment
- `LAG()` for month-over-month retention calculation
- `NTILE(5)` for RFM quintile scoring
- Conditional aggregation for pivot-style cohort matrix

## Dataset
UCI Online Retail II (UK e-commerce, 2009-2011, ~1M rows)

## Decision Log
[Link explaining: Why this definition of "retained"? What bias might exist?]

## 3-Minute Pitch
"I analyzed 1 million transactions to find which customer cohorts actually come back. The matrix shows that customers acquired in Q4 have 40% month-3 retention, while Q1 cohorts drop to 12%. Combined with RFM segmentation, I identified the top 20% of customers who drive 60% of revenue. This tells marketing: focus retention budget on Q4-acquired customers and replicate whatever campaign brought them in."
