# Lecture — Data Imputation

## Overview

This lecture covers the following four sections that build on each other:

- **A. Why Missing Data Matters for Machine Learning**. What data imputation is, how missing values degrade model performance, and why the handling strategy matters
- **B. How to Handle Missing Data**. The three top-level strategies: dropping, treating as a category, and replacing with an estimated value
- **C. Replacement Methods in Practice**. Mean/median/mode, K-nearest neighbors, and regression imputation with worked examples
- **D. Best Practices**. Choosing the right method based on data characteristics, and marking imputed values for traceability

## Learning Objectives

By the end of this lecture, you will be able to:

- Explain how missing data degrades model performance through reduced accuracy, biased inferences, and imbalanced representations
- Choose between dropping, encoding-as-category, and replacing missing values based on data type and missingness pattern
- Apply mean/median/mode, KNN, and regression imputation appropriately depending on distribution, outliers, and amount missing
- Mark imputed rows with indicator columns to maintain transparency and traceability in ML pipelines

## Why Missing Data Matters for Machine Learning

### What Is Data Imputation?

**Data imputation** is the process of filling in missing values with estimated or predicted values.



### Problems Caused by Missing Data

The right strategy depends on **why** the data is missing (random vs. systematic) and **how much** is missing.

| Problem                        | Why It Hurts the Model                                       |
| :----------------------------- | :----------------------------------------------------------- |
| **Reduced performance**        | Many algorithms (e.g., gradient-based) cannot accept nulls at all |
| **Biased inferences**          | If "missing" correlates with the target (e.g., wealthy customers skip income surveys), dropping rows skews the model |
| **Imbalanced representations** | Subgroups with more missing data become underrepresented     |
| **Complexity in handling**     | Each ML library and pipeline stage needs its own missing-value strategy |



## How to Handle Missing Data

There are three top-level strategies on how to handle missing data. Most projects use a mix.

* Drop Rows / Columns
* Treat as a Category
  * Encode "missing as own value"
  * Fill with "No"
* Replace (Inpute)

### Drop Columns or Rows

Use when there is too little signal to be worth recovering:

- **Drop a column** if it is missing in most rows
- **Drop a row** if most of its fields are missing

### Treat as a Category

For **categorical** columns, "missing" can be meaningful — for example, a customer who did not select a payment method or skipped a survey question.

A common approach is to encode missing values as a separate category, such as `"Unknown"` or `"Missing"`. This preserves information and allows models to learn from missingness.



### Replace with an Estimated Value (Impute)

For **numerical** columns, dropping is often too aggressive, and a simple category label doesn't capture the underlying value — so we estimate a replacement.

## Replacement Methods in Practice

### Mean / Median / Mode

The simplest method: replace each missing value with a summary statistic computed from the same column.

| Statistic  | Use For                              | Notes                                                        |
| :--------- | :----------------------------------- | :----------------------------------------------------------- |
| **Mean**   | Numeric, roughly normal distribution | Sensitive to outliers; reduces variance and can weaken correlations |
| **Median** | Numeric, skewed or with outliers     | Robust to outliers; also reduces variance and can weaken correlations |
| **Mode**   | Categorical                          | Most common value; can overinflate the majority class        |

  

## Conclusion

In this lecture, you covered the key concepts and techniques for handling missing data in machine learning pipelines:

1. **Why Missing Data Matters** — missing values degrade model performance, introduce bias, create imbalanced representations, and add complexity to every pipeline stage.
2. **How to Handle Missing Data** — the three top-level strategies are dropping rows or columns, treating missingness as a category, and replacing with an estimated value (imputation); most projects use a mix.
3. **Replacement Methods in Practice** — mean/median/mode imputation is simple but insensitive to relationships between features; KNN captures inter-column dependencies; regression imputation models uncertainty directly.
4. **Best Practices** — the right method depends on data type, distribution, amount missing, and domain knowledge; always mark imputed values with indicator columns for transparency and traceability.