# E-commerce Sales Funnel Analysis (SQL / Google BigQuery)

Analysis of user behaviour in an e-commerce funnel: where users drop off, which traffic sources convert best, how long conversion takes, and how much revenue each visitor generates.


## Goal

Answer four business questions with SQL:

1. How many users reach each stage of the purchase funnel, and where is the biggest drop-off?
2. Which traffic sources convert best, and at which stage do the weaker ones lose users?
3. How long does it take a user to go from first view to purchase?
4. How much revenue does the funnel generate per buyer and per visitor?

## Dataset

- **Size:** 9,381 events from 5,000 unique users
- **Period:** 30 Dec 2025 - 3 Feb 2026
- **Columns:** `event_id`, `user_id`, `event_type`, `event_date`, `product_id`, `amount`, `traffic_source`
- **Funnel stages (`event_type`):** `page_view` → `add_to_cart` → `checkout_start` → `payment_info` → `purchase`
- **Traffic sources:** organic, paid_ads, email, social

## Tools and SQL techniques

Google BigQuery (GoogleSQL), CTEs, conditional aggregation (`COUNT(DISTINCT CASE WHEN ...)`), `GROUP BY` / `HAVING`, `TIMESTAMP_DIFF`, `ROUND`, `SAFE_DIVIDE`.

## Key findings

### 1. Funnel overview

| Stage | Users | Step conversion |
|---|---|---|
| Page view | 5,000 | - |
| Add to cart | 1,553 | 31.1% |
| Checkout start | 1,103 | 71.0% |
| Payment info | 899 | 81.5% |
| Purchase | 826 | 91.9% |

**Overall conversion: 16.5%.** The largest drop-off is between viewing and adding to cart (only 31% progress). Once users start checkout, most complete the purchase.

### 2. Conversion by traffic source

| Source | Views | Purchases | View → cart | Cart → purchase | Overall |
|---|---|---|---|---|---|
| Email | 522 | 177 | 62.5% | 54.3% | 33.9% |
| Paid ads | 968 | 204 | 37.0% | 57.0% | 21.1% |
| Organic | 2,038 | 343 | 32.8% | 51.3% | 16.8% |
| Social | 1,472 | 102 | 13.6% | 51.0% | 6.9% |

Once a user has a cart, every source converts at a similar 51-57%. The difference between sources comes from the **view-to-cart step**: email visitors add to cart at 62.5% while social visitors do so at only 13.6%. This points to a landing-page or audience-fit problem for social traffic rather than a checkout problem.

### 3. Time to conversion (purchasers only, n = 826)

| Step | Average minutes |
|---|---|
| View → cart | 11.2 |
| Cart → purchase | 13.5 |
| View → purchase | 24.6 |

### 4. Revenue

- Total revenue: **87,975** from 826 orders
- Average order value: **106.51**
- Revenue per visitor: **17.60**

## Repository structure

```
├── README.md
├── data/
│   └── user_events.csv
├── sql/
│   └── funnel_analysis.sql
└── images/
    └── (query result screenshots)
```

## How to reproduce

1. Upload `data/user_events.csv` to BigQuery as a table (e.g. `user_events.1`).
2. Replace the table name in `sql/funnel_analysis.sql` with your own project, dataset and table.
3. Run the queries one section at a time.

## Limitations

- The funnel counts **distinct users per event type**. It does not check that events happened in order, which is acceptable for this dataset but would need a sequence check on real data.
- Every buyer has exactly one order, so average order value equals revenue per buyer and **repeat purchase / retention cannot be analysed**.
- The dataset is a sample, so the findings illustrate the method and are not business evidence.
