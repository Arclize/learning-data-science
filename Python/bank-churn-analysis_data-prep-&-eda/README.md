# Customer Churn Analysis: Data Preparation & EDA

## Objective
Understand customer **churn (Exited)** drivers through systematic data cleaning and exploratory analysis, and produce clear, reproducible visualizations comparing **churners vs. non‑churners** across key numeric and categorical features.

---

## Data Preparation

### ✅ Currency & Mixed Formats → Numeric
- Cleaned `Balance` by removing currency symbols and thousands separators across multiple formats (e.g., `$1,234.56`, `€0.0`, `£2,100`).
- Converted to numeric using `errors='coerce'` to safely handle edge cases.

### ✅ Binary Encoding
- Mapped `HasCrCard` from `'Yes'/'No'` to `1/0`, ensuring a numeric dtype for modeling and aggregation.

### ✅ Negative Values Handling
- Replaced negative `EstimatedSalary` entries with the **column median** (option to preserve integer dtype when required).

### ✅ Missing Values Imputation
- For numeric features (`Balance`, `NumOfProducts`, `CreditScore`, `Age`, `Tenure`, `EstimatedSalary`), imputed **NaNs with their respective column medians** (column-wise operation), ensuring robustness without distorting distributions.

### ✅ Merge Hygiene
- Addressed duplicate column names produced during merges (`Tenure_x` / `Tenure_y`) via:
  - Custom suffixes
  - Pre-merge column drops
  - Post-merge coalescing into a single `Tenure` field

### ✅ Duplicate & NaN Audits
- Identified rows containing missing values with:
  ```python
  df.isna().any(axis=1)
  ```
- Reviewed duplicates via:
  ```python
  df.duplicated()
  ```

---

## Exploratory Data Analysis (EDA)

### 📊 Churn Rate & Class Balance
- Computed overall churn count and proportion.
- Produced a **bar chart** of churn vs. non‑churn counts to confirm class imbalance and set expectations for model evaluation.

### 📈 Categorical Drivers of Churn
- **Churn % by Geography and Gender**:
  - Calculated category-wise churn rates using:
    ```python
    df.groupby('Geography')['Exited'].mean() * 100
    ```
  - Visualized with bar charts.
  - **Interpretation:** Highlights geographies and gender segments with elevated churn likelihood.

### 📉 Numeric Distributions by Churn Status
- **Box Plots (Churners vs. Non-churners)**:
  - For each numeric variable (`Balance`, `NumOfProducts`, `CreditScore`, `Age`, `Tenure`, `EstimatedSalary`), plotted side-by-side box plots to compare medians, interquartile ranges, and outliers across churn status.
  - **Interpretation:** Quickly surfaces shifts in central tendency and spread between classes.

- **Histograms (Overlay or Faceted)**:
  - Built histograms per numeric variable, broken out by churn status (using `hue='Exited'` or overlaid series), with optional `stat='density'` to normalize for class-size differences.
  - **Interpretation:** Reveals distributional shape differences (skew, modality) that box plots may hide.

---

## Key Takeaways
- Certain **geographies** and one **gender segment** may exhibit higher churn percentages.
- Differences in **Age**, **NumOfProducts**, and **IsActiveMember** often correlate with churn behavior.
- `Balance` and `EstimatedSalary` distributions can be skewed and may benefit from normalization in modeling.
- Data quality steps (currency normalization, median imputation, negative-value handling) materially improved the reliability of downstream insights.

---

## Next Steps
- Feature engineering (e.g., ratios such as `Balance / EstimatedSalary`).
- Model-ready splits and baseline classifiers with stratified evaluation (precision/recall, ROC-AUC), mindful of class imbalance.
