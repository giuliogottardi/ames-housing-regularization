# Ames Housing Price Prediction: OLS, Ridge, LASSO and Elastic Net

## Objective

This project models residential sale prices in Ames, Iowa, using a
curated set of structural, quality and location features (`Overall.Qual`,
`Gr.Liv.Area`, `Total.Bsmt.SF`, `Garage.Cars`, `Year.Built`,
`Neighborhood`, and others). The goal is to compare four linear modeling
approaches on the same data:

- **Ordinary Least Squares (OLS)**
- **Ridge Regression** (L2 penalty)
- **LASSO Regression** (L1 penalty)
- **Elastic Net** (L1 + L2, mixing parameter chosen by cross-validation)

The comparison covers both in-sample fit (R², significance of
coefficients) and **out-of-sample predictive performance** (test-set
RMSE on a held-out 20% split), and illustrates how L1/L2 regularization
behaves when the design matrix includes a high-cardinality categorical
predictor (`Neighborhood`, 28 levels, encoded as 27 dummy variables).

## Dataset

**Ames Housing dataset**, compiled by Dean De Cock (Truman State
University):

> De Cock, D. (2011). *"Ames, Iowa: Alternative to the Boston Housing
> Data as an End of Semester Regression Project."* Journal of Statistics
> Education, 19(3).

The dataset covers 2,930 residential property sales in Ames, Iowa,
between 2006 and 2010, and is publicly available — for example via
Kaggle's ["House Prices - Advanced Regression
Techniques"](https://www.kaggle.com/c/house-prices-advanced-regression-techniques)
competition. The raw CSV is not redistributed in this repository; see
"Reproducing the analysis" below for how to obtain it.

**Note on recency**: the underlying sales data are from 2006–2010 and do
not reflect current housing market conditions or price levels. This
project is a methodology exercise — data cleaning, regularized
regression, cross-validation, and out-of-sample validation — rather than
a current market pricing model.

## Methodology

1. **Data cleaning**: log-transform the target (`SalePrice` →
   `log_SalePrice`) to address right-skew and improve homoscedasticity;
   identify and exclude variables with structural missingness (e.g.
   `Pool.QC`, `Alley`, `Fence`, where `NA` means "feature absent" rather
   than "not recorded"); drop the small number of rows with genuine
   missing values (2 out of 2,930).
2. **OLS**: full linear model on all 14 selected predictors.
3. **Regularization**: Ridge, LASSO and Elastic Net fit via `glmnet`,
   with `lambda` (and, for Elastic Net, the mixing parameter `alpha`)
   chosen by 10-fold cross-validation.
4. **Model comparison**: coefficients compared across all four models;
   LASSO/Elastic Net variable selection examined against OLS
   significance.
5. **Out-of-sample validation**: 80/20 train/test split, test-set RMSE
   compared across all four models.

## Key Findings

- The full model explains a substantial share of the variance in
  `log_SalePrice` (R² ≈ 0.88), driven mainly by `Overall.Qual`,
  `Gr.Liv.Area`, `Total.Bsmt.SF`, `Year.Built`, `Garage.Cars` and
  `Neighborhood`.
- LASSO and Elastic Net eliminate a handful of weak `Neighborhood`
  dummies (thinly represented categories not statistically
  distinguishable from the baseline) along with `TotRms.AbvGrd`, which
  is redundant once `Gr.Liv.Area` is in the model.
- Ridge never sets coefficients exactly to zero, confirming that
  variable elimination is a genuine property of the L1 penalty rather
  than an artifact of cross-validation.
- Out-of-sample, all four models perform very similarly (test RMSE
  differences in the third decimal place on the log-price scale). With
  n = 2,928 and roughly 40 predictors, OLS does not overfit severely
  here, so regularization mainly improves interpretability and
  parsimony rather than raw predictive accuracy — a useful and honest
  finding in its own right.

## Reproducing the Analysis

1. Download `AmesHousing.csv` from
   [Kaggle](https://www.kaggle.com/c/house-prices-advanced-regression-techniques)
   (or the original source) and place it in the project root.
2. Open `AmesHousing_Project.Rmd` in RStudio.
3. Install dependencies if needed:
   ```r
   install.packages("glmnet")
   ```
4. Knit the document (`Ctrl+Shift+K` / `Cmd+Shift+K`) to reproduce all
   tables and plots.

## Tools

R, `glmnet` (Ridge/LASSO/Elastic Net), base R plotting.

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

## Author

Giulio Donato Gottardi — M.Sc. Financial Risk and Data Analysis, Sapienza
University of Rome.
