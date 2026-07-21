# Used Car Market Analysis with Python

![Cover](notebook/cover.png)

Exploratory Data Analysis and Feature Engineering on 42,708 used and damaged fuel-powered vehicles, investigating what features drive used car pricing and preparing a clean, ML-ready dataset for future predictive modeling.

## Objectives

**Phase 1 — EDA:**
- Understand dataset structure and detect data quality issues
- Clean and scope the data with justified, evidence-backed decisions
- Perform univariate analysis to understand individual feature distributions
- Perform bivariate and multivariate analysis to uncover relationships
- Generate business-relevant insights from the data

**Phase 2 — Feature Engineering:**
- Recreate the cleaned dataset from Phase 1
- Remove redundant, uninformative, and leaked features identified in EDA
- Encode categorical variables for ML compatibility
- Extract and test features from the `options` column
- Output a final ML-ready dataset

## Dataset

The original dataset contains 50,000 rows and 25 columns sourced from Kaggle, covering car identity (brand, model, year), condition (mileage, accident history), engine specs (horsepower, torque, fuel type), and pricing (price, price per km).

After scoping, the working dataset was refined to 42,708 rows by removing:
- New cars (almost all missing priceperkm, outside project scope)
- Electric vehicles (reported fuel efficiency as 0 — not comparable to fuel-powered cars)

## Setup

This project uses a Python virtual environment to keep dependencies isolated.

```bash
git clone https://github.com/huzaifa-khan-AiMl/Used-Car-Market-Analysis-with-Python.git
cd Used-Car-Market-Analysis-with-Python

python3 -m venv car_project_env
source car_project_env/bin/activate

pip install -r requirements.txt

jupyter lab
```

Open `notebook/car_price.ipynb` for the EDA (Phase 1), then `notebook/feature_engineering.ipynb` for feature engineering (Phase 2), and run the cells from top to bottom in each.

## Cleaning Decisions

A few cleaning steps involved judgment calls, not just routine fixes:

- Column names were lowercased, stripped of extra whitespace, and renamed to remove embedded units (e.g. `mileage(km)` → `mileage`).
- Duplicate rows were checked across the full row rather than a single column, since single-column matches can occur by coincidence in continuous numeric fields.
- Missing `priceperkm` values were dropped rather than imputed. Verification confirmed 2,454 of 2,459 missing values belonged to New cars, with only 5 belonging to Used cars — dropping these removed New cars (out of scope) and a negligible number of incomplete Used records.
- Electric vehicles were removed since their `fuelefficiency` value of 0 is structural (not a missing value) and not comparable to fuel-powered cars.
- Binary categorical columns (`accidenthistory`, `insurance`, `registrationstatus`) were converted to proper booleans for easier filtering and analysis.

## Key Findings (Phase 1 — EDA)

- Mileage and car age are the strongest negative price predictors (correlation -0.58 each) — both act as price ceilings, limiting how expensive a car can be
- Horsepower acts as a price floor — low HP reliably limits price downward, but high HP alone does not guarantee a high price
- Accident history significantly reduces price; luxury-tier cars (above ~$95,000) rarely carry accident history in this dataset
- Brand shows a clear two-tier market: Porsche, Mercedes-Benz, Audi, and BMW command medians above 16,000 USD, while mainstream brands sit under 8,000 USD
- Insurance and transmission have negligible effect on price and can be safely excluded as predictors in future ML work
- Engine size and torque correlate at 0.97 — essentially redundant features for ML modeling; only one is needed
- Diesel cars show the highest median price despite being the smallest group, but are completely absent from the ultra-luxury tier (above ~$95,000)
- Car age depreciation is remarkably consistent — each additional year removes roughly 5,000–8,000 USD from the maximum possible price
- Interior material, exterior color, and city show negligible effect on price and can be safely excluded as predictors
- Drivetype shows a substantial effect — FWD vehicles are notably cheaper (median ~5,658) than AWD/RWD (median ~13,434–14,624)
- Doors and seats both show meaningful price relationships, likely tied to body style and vehicle category
- Registration status shows negligible effect on price and can be safely excluded as a predictor

## Phase 2: Feature Engineering

Building on the EDA above, `notebook/feature_engineering.ipynb` prepares the dataset for machine learning:

- **Dropped for multicollinearity:** `carage` (1.00 correlation with mileage), `torque` (0.97 correlation with enginesize)
- **Dropped as uninformative:** `transmission`, `insurance`, `interior`, `color`, `city`, `registrationstatus`
- **Dropped for data leakage:** `priceperkm` — directly derived from `price` (price/mileage)
- **Dropped for dimensionality:** `model` (40 unique values) — retained `brand` as the broader, more generalizable signal
- **Feature extraction:** tested 8 flags parsed from the `options` column (Navigation, Cruise Control, Heated Seats, Parking Sensors, Bluetooth, Touchscreen, Sunroof, Rear Camera) — none showed a meaningful price relationship, so both the raw column and all extracted flags were dropped
- **Encoding:**
  - `condition` — label encoded (Used=0, Damaged=1), reflecting a genuine ordinal relationship
  - `brand`, `fueltype`, `drivetype`, `bodytype` — one-hot encoded, since none have a natural order
- **Kept as-is:** `year`, `mileage`, `enginesize`, `horsepower`, `doors`, `seats`, `accidenthistory`, `fuelefficiency`, `price`

**Final dataset:** 42,708 rows, 34 columns, fully numeric/boolean, exported as `data/car_price_features.csv`.

## Limitations

- Analysis is scoped to used and damaged fuel-powered vehicles only — conclusions do not apply to new or electric vehicles
- Dataset sourced from Kaggle may carry regional bias; different regions have different pricing dynamics not captured here
- Some brands (Fiat, Peugeot, Dacia) and Diesel fuel type have small sample sizes, limiting the reliability of conclusions involving these groups
- Correlations found in this analysis do not imply causation — other unobserved factors may contribute to the relationships identified
- The `options` column's absence-means-absence assumption could not be fully verified —some listings may be incomplete rather than exhaustive

## Future Work

- **Build a predictive ML model:** train a regression model on the cleaned, feature-engineered dataset — mileage, brand, horsepower, and condition are expected to be the strongest predictors
- **Feature scaling:** deferred until model selection, since the right approach (e.g. StandardScaler for linear/KNN models, unnecessary for tree-based models) depends on the chosen algorithm
- **Feature importance review:** once a model exists, check whether borderline features (doors, seats, drivetype) meaningfully contribute
- **Expand scope:** include electric vehicles and new cars as separate analyses to compare pricing dynamics across all vehicle types

## View Online

📓 Kaggle Notebook: https://www.kaggle.com/code/huzaifakhan200/used-cars-data-analysis/notebook

## Tools

Python, Pandas, NumPy, Matplotlib, Seaborn, JupyterLab — developed in WSL2 (Ubuntu).
