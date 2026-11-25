# Olist Delivery ETA Prediction

Exploratory analysis and delivery-time modeling for Olist's Brazilian e-commerce orders. The project examines how route, purchase timing, shipping cost, product characteristics, and Olist's checkout estimate relate to delivery duration across about 110K order-item records from 2017–2018.

Built with R, `tidyverse`, `tidymodels`, and [Quarto](https://quarto.org/).

## Key questions

- What factors are associated with longer delivery times?
- How accurately can delivery duration be predicted from information available at or near purchase?
- How often does Olist's estimated delivery date differ from actual delivery?
- Which routes and purchase periods deserve additional delivery buffers?

## Main findings

- Route is the strongest overall driver: seller state, customer state, and same-state/cross-state travel explain much more variation than purchase weekday or time.
- A richer feature set improves prediction over simple benchmarks, while linear, ridge, lasso, and decision-tree models perform comparatively closely.
- The decision-tree model had the lowest test MAE in the project, but the model still under-predicts unusually slow deliveries.
- Olist's own checkout estimate is useful, but adds limited extra accuracy once route and order features are included.
- The project recommends route-aware delivery promises and delivery windows rather than relying on a single point estimate.

## Website

The Quarto website is organized into:

- **Home** — project purpose and overview
- **Data** — loading, joining, cleaning, and validating six source tables
- **Exploration** — delivery performance, geography, timing, shipping cost, and product patterns
- **Modeling** — leakage-safe grouped splits, feature recipes, model tuning, and test-set evaluation
- **Conclusion** — route-level findings, recommendations, limitations, and future work

Rendered HTML, `_site/`, report, and slide files are included for convenient viewing.

## Repository structure

| Path | Purpose |
|---|---|
| `index.qmd` | Project homepage |
| `data.qmd` | Data loading and cleaning pipeline |
| `eda.qmd` | Exploratory data analysis |
| `modeling.qmd` / `modeling_v2.qmd` | Modeling workflow and experiments |
| `conclusion.qmd` | Results, recommendations, and limitations |
| `report.qmd` | Standalone report |
| `slides.qmd` | Presentation slides |
| `_quarto.yml` | Quarto website configuration |
| `_site/` | Rendered website output |
| `ML_Project.Rproj` | RStudio project file |

## Reproducing the analysis

### 1. Prerequisites

Install:

- R
- RStudio or another R-compatible environment
- Quarto

The project uses packages including `tidyverse`, `tidymodels`, `lubridate`, `glmnet`, `rpart`, `knitr`, and `corrplot`.

### 2. Prepare the data

The Olist CSV files are not included in this repository. Place the following files in a local `data/` folder:

- `customers.csv`
- `category_translation.csv`
- `order_items.csv`
- `orders.csv`
- `products.csv`
- `sellers.csv`

Open `ML_Project.Rproj` in RStudio, then set the project directory as your working directory if needed.

### 3. Render the website

```bash
quarto render
```

The rendered website will be written to `_site/`. The default navigation is configured in `_quarto.yml`.

### 4. Render individual outputs

```bash
quarto render report.qmd
quarto render slides.qmd
```

## Modeling approach

The analysis uses order-level grouping to keep multiple items from the same order in only one split. A 20% test set is held out, and a validation set is taken from the remaining training data for model tuning.

Three feature recipes progressively add:

1. Route and purchase-timing features
2. Product, freight, order, and listing features
3. Olist's own estimated delivery duration

Benchmarks and model families include:

- Naive average delivery time
- Olist estimate-only linear regression
- Linear regression
- Ridge regression
- Lasso regression
- Decision tree

## Data and model cautions

- Results use an order-item row as the analytical grain, while several fields repeat across items in the same order.
- The test split is random within the 2016–2018 data rather than a strict future-time holdout.
- Model accuracy is reported on average but is weaker for rare, unusually slow deliveries.
- Geographic features are state- or coarse-region-based; real seller-to-customer distance could improve the model.
- Coefficients and feature importance describe predictive associations, not causal effects.

## Author

Tanvi Aggarwal

[GitHub profile](https://github.com/Tanviaggarwal1211)
