# A Data-Driven Analysis of Movie Profitability

What makes a movie profitable? This project analyzes about 4,800 films from IMDb (1980–2020) to find out which production and release characteristics relate to profit, and then tests how well profit can be predicted using regression and machine learning.

**Author:** Deep Sanghvi

## Business Problem

Studios commit large budgets without knowing whether a film will recover its costs. A few titles earn exceptional profits while many do not break even. The project asks four questions:

1. Which production and release attributes are related to profit?
2. Do those effects hold after controlling for other factors?
3. How well can profit be predicted?
4. What does this mean for producers?

## Data

- **Source:** IMDb movie data (`data/IMDB_Movies.csv`), 5,438 rows covering films from about 1980 to 2020.
- **Fields:** title, MPAA rating, genre, release date, IMDb score, votes, director, writer, star, country, budget, gross, production company, runtime.
- **After cleaning:** 4,769 films.

### Cleaning and feature engineering

- Parsed release dates into year, month, day and season, plus a six-bucket day-of-month bin.
- Inflation-adjusted budgets at an average of 3.09% per year, then defined `profit = gross − adjusted budget`.
- Removed profit outliers with the IQR rule (640 rows), and dropped rows with unparseable dates or missing critical values.
- Added `movie_name_length` and `years_since_production`.
- Dummy-coded categorical variables with reference categories: Unrated (rating), Biography (genre), December (month) and days 26–31 (day bin).

## Methods

| Part | What it does |
|---|---|
| A. Cleaning and preparation | Date parsing, inflation adjustment, outlier removal, dummy coding, summary statistics, correlations |
| B. Descriptive analysis | Average profit, cost and gross by company, country, genre, rating, month, season and day bin; scatterplots of score, votes and runtime against profit |
| C. Multiple linear regression | Unstandardized and standardized OLS with profit as the dependent variable; nine hypotheses tested |
| D. Predictive modeling | Decision Tree Regressor vs. Neural Network (MLP) on a 70/30 train/test split, with gross and adjusted budget excluded to avoid leakage |

## Key Findings

- **Audience engagement (votes) is the strongest predictor of profit.** It is also the strongest effect in the regression.
- **Newer films are more profitable.** Profit declines with years since production.
- **Genre:** Comedy (about +$20.7M) and Horror (about +$19.1M) are significantly more profitable than the baseline genre once other factors are controlled. Only 2 of 14 genres differ significantly.
- **Timing:** September, October and November releases significantly underperform. The summer advantage visible in the raw averages is not significant after controls.
- **Ratings:** MPAA rating has no significant independent effect once other variables are controlled, even though PG and PG-13 look strongest in the descriptive view.
- **Score and runtime:** Higher IMDb score has a positive effect and longer runtime a small negative one, both modest in size.

### Model performance

| Model | Test R² | Test RMSE |
|---|---|---|
| Multiple linear regression (in-sample) | 0.253 (adj. 0.246) | – |
| Decision Tree (best: depth 5, min leaf 20) | 0.273 | ≈ $41.0M |
| Neural Network (MLP, best: 32-16 hidden layers) | 0.013 | ≈ $47.8M |

The Decision Tree clearly outperforms the Neural Network, which barely beats predicting the mean. Even the best model explains only about a quarter of the variation in profit, which fits the inherent uncertainty of the film business.

The notebook also predicts profit for three hypothetical films. They all receive the same prediction (about $44.3M) because they share the same attributes and fall into the same tree leaf, so this is a demonstration of the workflow and not a meaningful forecast.

## Recommendations for Producers

- Lean toward genres with stronger profit profiles, such as Comedy and Horror.
- Aim for PG or PG-13 where the story allows, for the widest audience, though the regression shows rating alone is not a significant driver.
- Avoid the September–November window for major releases.
- Do not assume a larger budget guarantees profit once other factors are controlled.

## Limitations

- No data on marketing spend, competition, franchise status or streaming.
- Predictive power is modest (test R² of about 0.27).
- Inflation adjustment uses a single flat average rate.
- Worth exploring next: Random Forest, Gradient Boosting and other ensemble methods.

## Repository Structure

```
movie-profitability-analysis/
├── data/
│   └── IMDB_Movies.csv
├── notebooks/
│   └── Movie_Profitability_Analysis.ipynb    # full analysis, Parts A–D
├── report/
│   └── Movie_Profitability_Report.pdf        # written report
├── presentation/
│   └── A-Data-Driven-Analysis-of-Movie-Profitability.pptx
└── README.md
```

## How to Run

Requirements: Python 3 with `pandas`, `numpy`, `matplotlib`, `scipy`, `statsmodels` and `scikit-learn`.

```bash
pip install pandas numpy matplotlib scipy statsmodels scikit-learn jupyter
jupyter notebook notebooks/Movie_Profitability_Analysis.ipynb
```

The notebook loads the data with `pd.read_csv("IMDB_Movies.csv")`. Since the CSV lives in `data/`, change that line to `pd.read_csv("../data/IMDB_Movies.csv")` (or copy the CSV next to the notebook) before running.

## Tools

Python, pandas, NumPy, Matplotlib, SciPy, statsmodels, scikit-learn.
