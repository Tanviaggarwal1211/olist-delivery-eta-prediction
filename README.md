# Olist Delivery ETA Prediction

What drives the number of days an Olist order takes to reach the customer (`delivery_days`), and can Olist predict it at checkout well enough to show a more accurate delivery estimate? This project explores about 110K order-item records from 2016–2018 and models delivery time from route, purchase timing, shipping cost, product characteristics, and Olist's own checkout estimate.

**Live site:** https://olist-eta-prediction-ml-project.netlify.app

Built with R, `tidyverse`, `tidymodels`, and [Quarto](https://quarto.org/).

## Key questions

- What factors are associated with longer delivery times?
- How accurately can delivery duration be predicted from information available at or near purchase?
- How often does Olist's estimated delivery date differ from actual delivery?
- Which routes and purchase periods deserve additional delivery buffers?

## Main findings

- Route is the strongest overall driver: seller state, customer state, and same-state versus cross-state travel explain much more variation than purchase weekday or time of day.
- A richer feature set improves prediction over simple benchmarks, while linear, ridge, lasso, and decision-tree models perform comparatively closely.
- The decision-tree model had the lowest test MAE in the project, but the model still under-predicts unusually slow deliveries.
- Olist's own checkout estimate is useful, but adds limited extra accuracy once route and order features are included.
- The project recommends piloting a route-aware delivery estimate, starting with same-state routes, rather than relying on a single point estimate.

## Website

The Quarto website has five pages:

- **Home**: question, why it matters, and key takeaways
- **Data**: loading, joining, cleaning, and validating six source tables
- **Exploration**: the target, data checks, grouped summaries, and charts on geography, timing, shipping cost, and product patterns
- **Modeling**: leakage-safe grouped split, feature recipes, model tuning, and test-set evaluation
- **Conclusion**: route-level findings, recommendations, limitations, and future work

The rendered site is included in `_site/` for convenient viewing.

## Repository structure

| Path | Purpose |
|---|---|
| `index.qmd` | Home page |
| `data.qmd` | Data loading and cleaning pipeline; writes `data/df_master_clean.rds` |
| `eda.qmd` | Exploration |
| `modeling.qmd` | Modeling workflow; writes the saved model results used by the conclusion |
| `conclusion.qmd` | Results, recommendations, and limitations |
| `_quarto.yml` | Quarto website configuration |
| `_site/` | Rendered website output |
| `ML_Project.Rproj` | RStudio project file |

## Reproducing the analysis

### 1. Prerequisites

Install R, RStudio (or another R environment), and Quarto. The project uses packages including `tidyverse`, `tidymodels`, `lubridate`, `glmnet`, `rpart`, `knitr`, and `corrplot`.

### 2. Prepare the data

The Olist CSV files are not included in this repository. Download the [Olist dataset from Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) and place these files in a local `data/` folder:

- `customers.csv`
- `category_translation.csv`
- `order_items.csv`
- `orders.csv`
- `products.csv`
- `sellers.csv`

Open `ML_Project.Rproj` in RStudio so the project directory is the working directory.

### 3. Render the website

```bash
quarto render
```

The pages depend on each other, so they render in order: `data.qmd` creates the cleaned dataset, `modeling.qmd` uses it and saves the model results, and `conclusion.qmd` reads those results. The order is set under `project: render:` in `_quarto.yml`. The rendered site is written to `_site/`.

## Modeling approach

Delivery time repeats across items in the same order, so the data is split by order, which keeps all items of an order on one side of the split. A 20% test set is held out. A smaller validation set is taken from the remaining training data and is used only to choose settings (ridge and lasso penalty, decision-tree depth). The test set is scored once, with no cross-validation.

Three feature recipes progressively add:

1. Route and purchase-timing features
2. Product, freight, order, and listing features
3. Olist's own estimated delivery duration

Benchmarks and model families:

- Naive average delivery time
- Olist estimate-only linear regression
- Linear regression
- Ridge regression
- Lasso regression
- Decision tree

Post-delivery fields (`delayed_days`, `delivered_status`, order status, delivery date) are excluded from the predictors to avoid leakage.

## Data and model cautions

- Results use an order-item row as the analytical grain, while several fields repeat across items in the same order.
- The test split is random within the 2016–2018 data rather than a strict future-time holdout. 2016 and 2018 are partial years.
- Model accuracy is reported on average but is weaker for rare, unusually slow deliveries.
- Geographic features are state- or coarse-region-based; real seller-to-customer distance could improve the model.
- Coefficients and feature importance describe predictive associations, not causal effects.

## Author

Tanvi Aggarwal, OMIS 681. [GitHub profile](https://github.com/Tanviaggarwal1211)

**AI-use statement:** I used Claude (Anthropic) as a coding and writing assistant. I reviewed and ran the code and take responsibility for the analysis and its conclusions.
