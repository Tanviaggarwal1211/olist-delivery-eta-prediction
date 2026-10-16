# Olist Delivery ETA Prediction

Exploratory analysis of about 110K Olist e-commerce order-item records (2017-2018): delivery performance, shipping cost, and the product and logistics factors that relate to delivery time. Built in R (tidyverse, ggplot2) and published as a Quarto website.

## Pages

Home, Data, Exploration, and a report page rendered as HTML, PDF, and slides.

## Reproducing

Open `ML_Project.Rproj`, then render the site with `quarto render`. The dataset is not included in this repository (it lives in a local `data/` folder that is git-ignored), so the pages that read `data/*.rds` need that folder in place.
