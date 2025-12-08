# Nobel Laureates, Age at Award, and Human Development

**Authors:** Matteus Kalda and Karl Gregor Urmet  
**Course:** Introduction to Data Science (IDS)  
**Project type:** Exploratory data analysis + statistical modelling + basic machine learning

---

## Project motivation and goals

The aim of this project is to explore patterns in Nobel Prize awards and relate them to
broader country-level development indicators.

More concretely, we investigate:

- **Who receives the Nobel Prize and when?**  
  - How old are laureates when they receive the prize?  
  - How has this age changed over time?  
  - How do age patterns differ by **category**, **sex** and **world region**?

- **Country-level differences:**  
  - Which countries produce more Nobel laureates per capita?  
  - Is there a relationship between the **Human Development Index (HDI)** and
    Nobel laureates per million inhabitants?

- **Modelling:**  
  - Can we **explain** age at award using simple variables (category, sex, region, time)?  
  - Can a basic scikit-learn regression model predict age at award any better than
    a simple baseline?

Our main conclusion is that we can describe several clear patterns (e.g. older laureates
over time, strong differences between fields), but we **cannot** build a practically
useful model that would accurately predict a future laureate’s age from these
simple features alone.

---

## Repository contents

The main files in this repository are:

- `README.md` – this file.

- `E10_report.pdf`  
  Full written report describing the project, methods, results and discussion.

- `poster.pdf` (or similar)  
  Final poster used for the project presentation (built from the analyses in the notebooks).

- **Data files**
  - `nobel_prizes_1901-2025_cleaned.csv`  
    Cleaned Nobel laureate data from Kaggle (one row per laureate–prize).
  - `hdr_general.csv`  
    Country-level HDI and population data from UNDP/Kaggle.

- **Notebooks**
  - `01_nobel.ipynb`  
    Analysis at the **laureate level**:
    - data cleaning and data-quality checks for the Nobel dataset  
    - feature engineering:
      - `birth_year`, `age_at_award`, `decade`, `region`
    - exploratory data analysis:
      - age distribution and trends by decade  
      - comparisons by category, sex and region  
    - statistical modelling:
      - OLS regression for age at award using category, sex, region and time  
    - scikit-learn experiment:
      - regression models for age at award (LinearRegression, Ridge, Lasso)  
      - train/test split, MSE and R² comparison with the OLS model

  - `02_country_hdi.ipynb`  
    Analysis at the **country–decade level**:
    - data cleaning and data-quality checks for the HDI dataset  
      (range checks, duplicate checks, excluding “World” and regional averages)  
    - harmonising country names between Nobel and HDI data  
    - constructing country–decade table:
      - Nobel laureates per country and decade  
      - mean HDI and population per country and decade  
      - Nobel laureates **per million inhabitants**  
    - exploratory analysis of HDI levels and groups (Low/Medium/High/Very high)  
    - regression analysis relating HDI to Nobel laureates per capita  
      (including sensitivity analysis excluding microstates such as Saint Lucia)

The repository may contain additional helper code or experiments whose results do not
appear on the final poster; this is intentional and documents the amount of work done.

---

## How to reproduce the analysis

### 1. Requirements

You will need:

- Python 3.9+  
- The following Python packages (install via `pip` or `conda`):

  - `pandas`  
  - `numpy`  
  - `matplotlib`  
  - `seaborn`  
  - `statsmodels`  
  - `scikit-learn`  
  - `jupyter`

For example, you can install everything with:

    pip install pandas numpy matplotlib seaborn statsmodels scikit-learn jupyter

### 2. Getting the data

Place the following files in the **same directory** as the notebooks:

- `nobel_prizes_1901-2025_cleaned.csv`
- `hdr_general.csv`

(If you clone the repository as-is, these files should already be present; otherwise,
download them from the sources referenced in the report and save them with the same
filenames.)

### 3. Running the notebooks

1. Start Jupyter:

       jupyter notebook

2. Open and run the notebooks in this order:

   1. `01_nobel.ipynb`  
      - Run all cells (`Kernel → Restart & Run All`).  
      - This reproduces all laureate-level analyses and models used for the poster
        (EDA, OLS regression, scikit-learn regression).

   2. `02_country_hdi.ipynb`  
      - Run all cells.  
      - This reproduces the country–decade level analyses and the HDI–Nobel models.

Both notebooks are written so that they should execute from top to bottom on a fresh
kernel, assuming the required data files are available.

Random components (e.g. train/test splits for scikit-learn models) use fixed
`random_state` values (e.g. `random_state=7`), so results such as R² and accuracy
should be reproducible up to minor numeric differences.

---

## Guide to the code structure

The code is organised into a small number of well-separated blocks inside each notebook.

### `01_nobel.ipynb`

1. **Loading and inspecting data**  
   - Reads Nobel data and prints basic summaries.

2. **Data cleaning and feature engineering**  
   - Extracts birth year from messy birth date strings.  
   - Computes age at award and filters unrealistic ages.  
   - Creates a `decade` variable.  
   - Maps birth/affiliation countries to broad world regions.  
   - Checks for missing values and duplicate laureate–year–category rows.

3. **Exploratory data analysis (EDA)**  
   - Age distribution plots.  
   - Age by category (boxplots and tables).  
   - Median age by decade and category (line plots).  
   - Age by sex and region.  
   - Counts of laureates by decade and region.

4. **Age regression model (statsmodels)**  
   - Fits `age_at_award ~ C(category) + C(sex) + C(region) + decade`.  
   - Summarises coefficients and confidence intervals.  
   - Interprets which effects are large and which are small.

5. **Machine learning experiment: age regression (scikit-learn)**  
   - One-hot encodes categorical variables.  
   - Train/test split.  
   - Fits LinearRegression, Ridge and Lasso models.  
   - Compares MSE and R² on the test set to the OLS model and concludes that
     prediction performance is limited (R² well below 0.5), so the model is mainly
     descriptive rather than practically predictive.

### `02_country_hdi.ipynb`

1. **Loading and cleaning HDI data**  
   - Filters to real countries (excludes aggregated “World” and regional averages
     using the `iso3` codes).  
   - Ensures HDI is in a sensible range and population values are non-negative.  
   - Checks for duplicate `iso3–year` pairs and missing values.

2. **Building HDI and population summaries**  
   - Aggregates HDI and population by country and decade.  
   - Builds HDI groups (Low/Medium/High/Very high) based on the latest HDI per country.  
   - Plots HDI trends over time for selected countries.  
   - Explores the distribution of population sizes across countries and years and
     how median and maximum country population change over time.

3. **Linking Nobel and HDI data**  
   - Harmonises country names between Nobel and HDI datasets.  
   - Aggregates Nobel laureates by country and decade.  
   - Merges with the HDI decade table and computes Nobel laureates per million inhabitants.  
   - Checks how many countries appear in both datasets.

4. **HDI vs Nobel per capita modelling**  
   - Creates scatter plots of Nobel laureates per million vs HDI.  
   - Computes correlations.  
   - Fits simple OLS regressions of Nobel per million on HDI (and on HDI + decade).  
   - Performs sensitivity analyses (e.g. excluding Saint Lucia and other microstates).  
   - Concludes that the HDI–Nobel relationship is weak and that many other factors
     must be involved.

---

## Notes on non-poster results

The repository contains some analyses and experiments whose results are **not**
shown on the final poster, for example:

- alternative visualisations (e.g. population plots, extra EDA),
- extended machine learning experiments for age prediction,
- intermediate data-quality checks and diagnostics.

These are kept in the notebooks to document the full workflow and allow reviewers
to see the amount of work and the reasoning that led to the final poster.
