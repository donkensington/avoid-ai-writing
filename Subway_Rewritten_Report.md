# Subway Franchise Sales Analysis (Rewritten)

## 1. Introduction
Subway operates one of the largest franchise networks in fast food. Store performance differs widely across locations, so the company needs a practical way to estimate sales from local and operational inputs. This report analyzes data from 27 franchise locations using multiple linear regression.

The objective is to identify which factors are statistically linked to annual net sales and to provide a usable prediction model for decisions on site selection, advertising spend, inventory planning, and store size.

## 2. Problem Statement
Management asked two questions:

1. Which operational and demographic factors significantly affect annual net sales?
2. Given a specific set of store and neighborhood characteristics, what annual sales level can be predicted?

### 2.1 Assumptions
- The data represent one full fiscal year and are comparable across all 27 stores.
- Relationships between SALES and the predictors are approximately linear.
- Observations are independent (one row per store).
- Monetary values are in thousands of dollars ($000s), and area is in thousands of square feet.
- Advertising is treated as annual spend. In the new-store scenario, "$5,000" is interpreted as annual spend to match dataset scale.

## 3. Data Source
The dataset is `Franchises.xlsx` and includes one record per franchise location (`n = 27`), combining internal reporting with district-level demographic information.

## 4. Data Description
### 4.1 Variables
- **SALES**: annual net sales ($000s), dependent variable.
- **SQFT**: store size (000s sq ft).
- **INVENTORY**: inventory value ($000s).
- **ADVERTISING**: annual advertising spend ($000s).
- **FAMILIES**: families in the district (000s).
- **STORES**: number of competing stores in the district.

### 4.2 Descriptive Statistics
- SALES: mean 286.6, SD 192.1, min 0.5, median 341.0, max 570.0.
- SQFT: mean 3.33, SD 2.01, min 0.5, median 3.5, max 8.6.
- INVENTORY: mean 387.5, SD 191.2, min 102, median 382, max 788.
- ADVERTISING: mean 8.10, SD 3.77, min 2.5, median 8.1, max 17.4.
- FAMILIES: mean 9.69, SD 5.14, min 1.6, median 11.3, max 16.3.
- STORES: mean 7.74, SD 4.90, min 0, median 8, max 15.

## 5. Methods
### 5.1 Exploratory Linear Association
Each predictor had a strong linear relationship with SALES in simple regression, all with `p < 0.001`:
- SQFT: `r = 0.894`, `R² = 0.799`.
- INVENTORY: `r = 0.946`, `R² = 0.894`.
- ADVERTISING: `r = 0.914`, `R² = 0.835`.
- FAMILIES: `r = 0.954`, `R² = 0.910`.
- STORES: `r = -0.912`, `R² = 0.832`.

### 5.2 Transformation Check
A log-transformed dependent variable model (`ln(SALES)`) performed worse than the untransformed model:
- `R²` decreased from `0.993` to `0.716`.
- Shapiro-Wilk for residuals worsened (`p = 0.0002`).

No transformation was retained in the final model.

### 5.3 Final Multivariate Model

SALES = -18.86 + 16.20(SQFT) + 0.1746(INVENTORY) + 11.53(ADVERTISING) + 13.58(FAMILIES) - 5.311(STORES)

Coefficient summary:
- Intercept: -18.86 (`p = 0.538`).
- SQFT: 16.20 (`p = 0.0002`, VIF 4.24).
- INVENTORY: 0.1746 (`p = 0.0063`, VIF 10.12).
- ADVERTISING: 11.53 (`p = 0.0002`, VIF 7.62).
- FAMILIES: 13.58 (`p < 0.001`, VIF 6.91).
- STORES: -5.311 (`p = 0.0052`, VIF 5.82).

### 5.4 Model Fit
- ANOVA: `F(5, 21) = 611.6`, `p < 0.001`.
- `R² = 0.9932`, Adjusted `R² = 0.9916`.
- `RMSE = 14.76` ($000s).

Compared with the tested alternatives (log model and reduced 3-variable model), the full 5-variable model had the best performance.

### 5.5 Residual Diagnostics
Residual-vs-fitted plots showed no strong systematic pattern. The Q-Q plot showed mild lower-tail deviation. Shapiro-Wilk `p = 0.037` is borderline but acceptable for this small-sample exploratory model.

## 6. Results
### 6.1 Overall Model Significance
The full model is statistically significant and explains 99.3% of the variation in SALES.

### 6.2 Individual Predictor Effects
All five predictors are statistically significant:
- **FAMILIES**: +$13,580 per additional 1,000 families.
- **SQFT**: +$16,200 per additional 1,000 sq ft.
- **ADVERTISING**: +$11,530 per additional $1,000 annual spend.
- **INVENTORY**: +$175 per additional $1,000 in inventory.
- **STORES**: -$5,310 per additional competing store.

### 6.3 Multicollinearity
INVENTORY has `VIF = 10.12`, so its coefficient should be interpreted with caution because it overlaps with other operational variables.

### 6.4 Sales Prediction for New Store Scenario
Inputs:
- SQFT = 5
- INVENTORY = 250
- ADVERTISING = 5
- FAMILIES = 5
- STORES = 5

Prediction:

`SALES = -18.86 + 16.20(5) + 0.1746(250) + 11.53(5) + 13.58(5) - 5.311(5) = 204.8`

Projected annual net sales: **$204,785**.

If advertising increases from $5,000 to $10,000 annually (all else fixed), predicted sales increase by about **$57,865**.

## 7. Conclusion and Recommendations
1. **Prioritize district demand.** FAMILIES is the strongest predictor.
2. **Use advertising as a controllable input.** The model indicates a strong marginal sales response to additional spend.
3. **Align store size with expected demand.** Larger store footprints are associated with higher sales.
4. **Account for competitor density in site decisions.** More competing stores are associated with lower sales.
5. **Manage inventory efficiently.** Inventory is significant but has a smaller marginal effect and multicollinearity risk.

For the provided new-store profile, predicted annual sales are approximately **$204.8k**. Management should test scenarios with higher advertising spend and stronger district demand before final approval.
