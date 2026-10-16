# Lecture — Data Encoding

## Overview

This lecture covers the following five sections that build on each other:

- **A. Why Encoding Matters for ML**. Why most algorithms need numerical input, the three recurring categorical-feature challenges (high cardinality, missing values, string types), and practical solutions for each
- **B. One-Hot Encoding**. How it works, its drawbacks at high cardinality, and how to apply it in Spark ML with `StringIndexer` → `OneHotEncoder`
- **C. Label and Target Encoding**. Label encoding for ordinal features, target encoding for high-cardinality categoricals, and the risks of each approach
- **D. Embeddings**. What embeddings are, how they are produced, and how categorical embeddings transform discrete categories into dense, semantic vector representations
- **E. Conclusion**. Summary of when to use each encoding method and key takeaways

## Learning Objectives

By the end of this lecture, you will be able to:

- Explain why most ML algorithms require numerical input and why encoding categorical features is essential
- Identify and address the three main categorical-feature challenges: high cardinality, missing values, and string types
- Apply one-hot, label, and target encoding appropriately depending on feature type, cardinality, and model requirements
- Use Spark ML's `StringIndexer` and `OneHotEncoder` together in a pipeline with `handleInvalid="keep"`
- Recognize when categorical embeddings outperform classical encodings and describe the embedding lookup process

## Why Encoding Matters for ML

### Most Algorithms Need Numbers

The vast majority of ML algorithms — linear models, gradient boosting, neural networks — accept **numerical input only**. Categorical features like `Contract = "Month-to-month"` or `City = "Paris"` must be converted to numbers before training.

The sections below cover three encoding families — one-hot, label/target, and embeddings — each suited to different feature types and model requirements.

### Three Categorical-Feature Challenges

Categorical features present three recurring challenges in ML workflows. The table below summarizes each challenge, why it's difficult, and where it is typically addressed.

| The challenge        | The issue                                                    | The solution                                                 |
| :------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **High cardinality** | Many unique values in a categorical feature can lead to increased dimensionality and potential overfitting. | Group rare categories, top-N selection, target encoding, embeddings |
| **Missing values**   | Categorical features often have missing values, which need to be addressed before model training | Mode imputation, constant imputation, treat as a separate category |
| **String types**     | ML models reject strings. Models require numerical input, and categorical variables need to be encoded. | One-hot encoding, label encoding, target encoding, embeddings |

### High Cardinality Issues and Solutions

#### **Issue: Large set of categories**

Many unique values in a categorical feature lead to large numbers of dummy variables, increased sparsity, and potential overfitting — especially when some categories appear only a few times.

#### **Possible Solutions:**


**Group Rare Categories**

| Category | Count | Grouped |
| :------: | :---: | :-----: |
|  **1**   | 1200  |    1    |
|  **2**   |  980  |    2    |
|  **3**   |  870  |    3    |
|    4     |  12   |    0    |
|    5     |   8   |    0    |
|    6     |   5   |    0    |

Rare categories (count < threshold) are collapsed into a single group (0).



**Top-N Categories**

| Category | Count |   Kept?   |
| :------: | :---: | :-------: |
|  **1**   | 1200  |  ✓ Top 3  |
|  **2**   |  980  |  ✓ Top 3  |
|  **3**   |  870  |  ✓ Top 3  |
|    4     |  12   | ✗ Dropped |
|    5     |   8   | ✗ Dropped |
|    6     |   5   | ✗ Dropped |

Only the top-N most frequent categories are kept; the rest are removed or masked.



### Missing Values Issues and Solutions

**Issue: Categorical gaps**



## One-Hot Encoding

### How It Works

One-hot encoding adds **one binary column per category**. Each row has a `1` in exactly one column and `0` in the rest.



|  ID  |     City     |  →   | New York | Paris | London |
| :--: | :----------: | :--: | :------: | :---: | :----: |
|  1   | **New York** |      |    1     |   0   |   0    |
|  2   |  **Paris**   |      |    0     |   1   |   0    |
|  3   |  **London**  |      |    0     |   0   |   1    |
|  4   |  **Paris**   |      |    0     |   1   |   0    |
|  5   | **New York** |      |    1     |   0   |   0    |



### Drawbacks

While one-hot encoding is widely used, it introduces several practical limitations:

- **Sparsity and memory overhead** — most entries are zero, inflating dataset size
- **Poor scaling with cardinality** — feature space explodes as the number of categories grows
- **Obscured feature importance** — a single concept is split across many columns

### In Spark ML: `StringIndexer` → `OneHotEncoder`

Spark ML one-hot encoding is a **two-step** process:

1. **`StringIndexer`** — converts strings to numeric indices (`"Month-to-month"` → `0.0`, `"One year"` → `1.0`, …)
2. **`OneHotEncoder`** — converts those indices to sparse binary vectors





## Label and Target Encoding

### Label Encoding (String Indexing)

`StringIndexer` assigns each category an integer **by frequency** — the most common category gets index 0, the next most common gets 1, and so on. The indices have no ordinal meaning.

|  ID  |      Contract      |  →   | Contract_idx |  Why this index?  |
| :--: | :----------------: | :--: | :----------: | :---------------: |
|  1   | **Month-to-month** |      |      0       |   Most frequent   |
|  2   |    **One year**    |      |      1       | 2nd most frequent |
|  3   |    **Two year**    |      |      2       |  Least frequent   |
|  4   | **Month-to-month** |      |      0       |   Same as row 1   |

**When to use / not u**se

- **When to use** — In Spark ML, this is standard practice for nominal features fed into tree-based models such as Random Forest and GBT. Trees split on thresholds, so this approach is commonly used in Spark pipelines even though the labels are assigned arbitrarily.
- **Important nuance** — The encoded integers still impose an arbitrary numeric ordering, so this guidance is specific to Spark ML tree-model workflows and should not be generalized to sklearn-style pipelines or libraries with native categorical handling.
- **When NOT to use** — Linear models will infer a numeric relationship between indices (0 < 1 < 2) that doesn't exist. Outside Spark ML, other tree libraries may prefer one-hot encoding or native categorical support instead.



# References

* Data Encoding- Top #6: Databricks Certified Machine Learning Engineer- Associate: https://youtu.be/Kp-SwhV_1_M?list=PLubLCwMyFlZTTKv4OEKlMISAubBqJWLpx