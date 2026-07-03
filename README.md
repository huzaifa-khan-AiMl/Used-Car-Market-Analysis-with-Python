# Used Car Market Analysis with Python

![Cover](notebook/cover.png)

Exploratory Data Analysis on 42,708 used and damaged fuel-powered vehicles, investigating what features drive used car pricing and providing actionable insights for buyers, sellers, and dealerships.

---

## Objectives

- Understand dataset structure and detect data quality issues
- Clean and scope the data with justified, evidence-backed decisions
- Perform univariate analysis to understand individual feature distributions
- Perform bivariate and multivariate analysis to uncover relationships
- Generate business-relevant insights from the data

---

## Dataset

The original dataset contains 50,000 rows and 25 columns sourced from Kaggle, covering car identity (brand, model, year), condition (mileage, accident history), engine specs (horsepower, torque, fuel type), and pricing (price, price per km).

After scoping, the working dataset was refined to **42,708 rows** by removing:
- New cars (almost all missing `priceperkm`, outside project scope)
- Electric vehicles (reported fuel efficiency as 0 — not comparable to fuel-powered cars)

---

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

Open `notebook/car_price.ipynb` and run the cells from top to bottom.

---

## Cleaning Decisions

A few cleaning steps involved judgment calls, not just routine fixes:

- **Column names** were lowercased, stripped of extra whitespace, and renamed to remove embedded units (e.g. `mileage(km)` → `mileage`).
- **Duplicate rows** were checked across the full row rather than a single column, since single-column matches can occur by coincidence in continuous numeric fields.
- **Missing `priceperkm` values** were dropped rather than imputed. Verification confirmed 2,454 of 2,459 missing values belonged to New cars, with only 5 belonging to Used cars — dropping these removed New cars (out of scope) and a negligible number of incomplete Used records.
- **Electric vehicles** were removed since their `fuelefficiency` value of 0 is structural (not a missing value) and not comparable to fuel-powered cars.
- **`options` column** was intentionally left untouched — it is a free-text, comma-separated column with potential for future feature engineering (e.g. does having Navigation or Bluetooth affect price), but parsing it was out of scope for this pass.
- **Binary categorical columns** (`accidenthistory`, `insurance`, `registrationstatus`) were converted to proper booleans for easier filtering and analysis.

---

## Key Findings

- **Mileage and car age** are the strongest negative price predictors (correlation -0.58 each) — both act as price ceilings, limiting how expensive a car can be
- **Horsepower** acts as a price floor — low HP reliably limits price downward, but high HP alone does not guarantee a high price
- **Accident history** significantly reduces price; luxury-tier cars (above ~$95,000) almost never carry accident history in this dataset
- **Brand** shows a clear two-tier market: Porsche, Mercedes-Benz, Audi, and BMW command medians above 16,000 USD, while mainstream brands sit under 8,000 USD
- **Insurance and transmission** have negligible effect on price and can be safely excluded as predictors in future ML work
- **Enginesize and torque** correlate at 0.97 — essentially redundant features for ML modeling; only one is needed
- **Diesel cars** show the highest median price despite being the smallest group, but are completely absent from the ultra-luxury tier (above ~$95,000)
- **Car age depreciation** is remarkably consistent — each additional year removes roughly 5,000–8,000 USD from the maximum possible price

---

## Limitations

- Analysis is scoped to used and damaged fuel-powered vehicles only — conclusions do not apply to new or electric vehicles
- Dataset sourced from Kaggle may carry regional bias; different regions have different pricing dynamics not captured here
- Some brands (Fiat, Peugeot, Dacia) and Diesel fuel type have small sample sizes, limiting the reliability of conclusions involving these groups
- Correlations found in this analysis do not imply causation — other unobserved factors may contribute to the relationships identified

---

## Future Work

- **Feature engineering**: handle the near-perfect correlation between mileage and carage (1.00) and between enginesize and torque (0.97) before modeling
- **Drop low-value features**: transmission and insurance showed no meaningful relationship with price and can be excluded
- **Parse the `options` column**: extract features like Navigation, Bluetooth, or Cruise Control and test whether they affect price
- **Build a predictive ML model**: train a regression model on the cleaned dataset — mileage, carage, brand, horsepower, and condition are expected to be the strongest predictors
- **Expand scope**: include electric vehicles and new cars as separate analyses to compare pricing dynamics across all vehicle types

---

## Tools

Python, Pandas, NumPy, Matplotlib, Seaborn, JupyterLab — developed in WSL2 (Ubuntu).
