# Segment-Conditional Drivers of Post-Purchase Dissatisfaction in E-Commerce

A two-stage data-mining pipeline that combines **K-means clustering** with
**association-rule mining (Apriori)** to show that the drivers of customer
dissatisfaction differ by order type — a pattern a single global model averages away.

Built on the public [Olist Brazilian E-Commerce dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
(~96k delivered orders, 2016–2018).

## The idea

Standard e-commerce analytics asks "what predicts a bad review?" globally, across all
orders. But a late delivery on a £700 order is not the same event as a late delivery on
a £30 order. This project:

1. **Segments orders** into five fulfilment-experience profiles using K-means on five
   engineered features (wait time, delivery reliability, relative freight cost, basket
   size, order value).
2. **Mines dissatisfaction rules inside each segment** using Apriori, rather than once
   over the whole dataset.

## Headline finding

Late delivery lifts dissatisfaction by **~1.5×** in the high-delay segment but
**~3.7×** in the premium (high-value) segment. The same broken promise does far more
damage to an expensive order — a segment-conditional effect invisible to a global
analysis. Three of the five segments yield no dissatisfaction rule at all, which itself
tells a retailer where problems do *not* systematically concentrate.

## Method highlights

- **Feature engineering** from raw timestamps and costs, aggregated to order level.
- **Winsorising** at the 1st/99th percentile to control extremes without discarding the
  "slow but real" orders that are the point of the study.
- **Standardisation** before clustering so no feature dominates the distance metric by
  virtue of its units.
- **k chosen** via elbow + silhouette diagnostics (k=5).
- **Stability validated** across five random seeds (mean Adjusted Rand Index ≈ 0.998).
- Dissatisfaction (review score ≤ 2) attached *after* clustering as an independent check
  — it ranges 7%–40% across segments the model built blind to reviews.

## Repository contents

| File | What it is |
|---|---|
| `olist_dissatisfaction_analysis.ipynb` | The full annotated analysis, runs top to bottom |
| `olist_analysis_dataset.xlsx` | Derived order-level dataset with cluster labels + data dictionary |
| `olist_order_features_clustered.csv` | Same data as CSV |
| `requirements.txt` | Python dependencies |

## Running it

```bash
pip install -r requirements.txt
```

Download the Olist CSVs from Kaggle and place these five in a `data/` folder:

```
data/olist_orders_dataset.csv
data/olist_order_items_dataset.csv
data/olist_products_dataset.csv
data/olist_order_reviews_dataset.csv
data/product_category_name_translation.csv
```

Then open the notebook and run all cells.

> The raw Olist CSVs (~100 MB) are **not** committed here — download them from Kaggle at
> the link above. The `data/` folder is gitignored.

## Limitations

The data is 2016–2018 and from a single Brazilian marketplace, so the specific
coefficients are not universal — the *method* transfers, the numbers may not. Review
score is a proxy for dissatisfaction, not a direct returns measure. Mixed-category
baskets are simplified to their first category.
