Of course\! Here is an updated and comprehensive `README.md` file that reflects the changes and final results in your modified Jupyter Notebook.

-----

# Credit Card Fraud Detection with Advanced Sampling Techniques

## Objective

This project tackles the critical issue of class imbalance in a credit card fraud dataset. The primary goal is to build and evaluate robust fraud detection models using advanced sampling techniques. Specifically, this analysis employs a **Gaussian Mixture Model (GMM)** for oversampling and a **hybrid approach combining GMM with Clustering-Based Undersampling (CBU)**. The performance of these methods is rigorously compared against a baseline Logistic Regression model trained on the original, imbalanced data to demonstrate their effectiveness in identifying fraudulent transactions.

-----

## Dataset

  - **Source**: `creditcard.csv`
  - **Description**: The dataset contains credit card transactions featuring PCA-transformed variables, with 'Time' and 'Amount' being the exceptions. The target variable, 'Class', indicates whether a transaction is fraudulent.
  - **Key Challenge: Severe Class Imbalance**
      - **Class 0 (Non-Fraudulent)**: 284,315 samples (99.83%)
      - **Class 1 (Fraudulent)**: 492 samples (0.17%)
      - **Imbalance Ratio**: The dataset exhibits an extreme imbalance ratio of approximately **578:1**, posing a significant challenge for model training. Models trained on this data are naturally biased towards the majority class, leading to poor detection rates for fraudulent transactions.

-----

## Methodology

The project follows a systematic workflow, starting with a baseline model and progressing to the implementation and evaluation of advanced sampling solutions.

### Part A: Baseline Model & Data Analysis

1.  **Exploratory Data Analysis**: The initial analysis confirmed the severe class imbalance through bar and pie charts, quantifying the skewed distribution.
2.  **Data Splitting**: The dataset was divided into training (70%) and testing (30%) sets using a stratified split to preserve the original class proportions in both subsets.
3.  **Baseline Model**: A standard **Logistic Regression** model was trained on the original, imbalanced training data. This model serves as a benchmark to measure the impact of our sampling techniques.
      - **Performance**: While achieving high overall accuracy (99.9%), the baseline model's performance on the minority (fraud) class was suboptimal, with a **Recall of only 61.49%**. This indicates it failed to identify nearly 39% of fraudulent transactions.

### Part B: Gaussian Mixture Model (GMM) for Synthetic Sampling

This section details the use of GMMs, a generative approach, to create synthetic data for the minority class.

1.  **Theoretical Foundation (GMM vs. SMOTE)**:

      - Unlike SMOTE, which **interpolates** between existing minority points, a GMM **generates** new samples by learning the underlying probability distribution of the data.
      - GMM is superior for complex data structures because it can model multi-modal distributions and non-convex shapes, whereas SMOTE's linear approach might create unrealistic "bridge" samples in these scenarios.

2.  **GMM Implementation**:

      - A GMM was trained exclusively on the minority (fraud) class data from the training set.
      - The optimal number of Gaussian components (`k`) was determined by comparing the **Bayesian Information Criterion (BIC)** and **Akaike Information Criterion (AIC)**.
      - The BIC score suggested an optimal `k` of **5**, which was chosen for the final model due to its more conservative approach and clearer "elbow" point in the analysis plot.

3.  **Model 2: GMM Oversampling**:

      - The trained GMM (with `k=5`) was used to generate new synthetic fraud samples.
      - The training set was fully balanced by generating enough new samples to make the minority class count equal to the majority class count (199,020 samples each).

4.  **Model 3: Hybrid Rebalancing (GMM + CBU)**:

      - A hybrid strategy was implemented to create a smaller, more efficient, yet balanced dataset.
      - **Clustering-Based Undersampling (CBU)**: The majority (non-fraud) class was undersampled using `KMeans`. Cluster centroids were selected as representative points, reducing the majority class size from 199,020 to 688.
      - **GMM Oversampling**: The GMM then oversampled the minority class to match the new, smaller majority class size, resulting in a perfectly balanced dataset with 688 samples for each class.

### Part C: Performance Evaluation and Conclusion

The two new Logistic Regression models were trained on the GMM-oversampled data (Model 2) and the GMM+CBU hybrid data (Model 3). Their performances were evaluated on the original, imbalanced test set and compared to the baseline.

1.  **Comparative Analysis**: The key performance metrics for detecting the minority (fraud) class were compiled and compared across all three models.

| Model | Precision | Recall | F1-Score |
| :--- | :--- | :--- | :--- |
| **Baseline (Imbalanced)** | 85.85% | 61.49% | 71.65% |
| **GMM Oversampling** | 8.01% | 85.81% | 14.65% |
| **GMM + CBU Hybrid** | 5.51% | **83.11%** | 10.34% |

2.  **Analysis of Results**:

      - Both GMM-based strategies significantly boosted **Recall** compared to the baseline, which is the primary goal in fraud detection (catching as many fraudulent transactions as possible).
      - The increase in Recall came at the cost of a sharp drop in **Precision**, leading to more false positives. This is a standard and often acceptable trade-off in imbalanced problems.
      - The **GMM Oversampling** model achieved the highest Recall (**85.81%**), successfully identifying over 85% of all fraudulent transactions in the test set.

3.  **Final Recommendation**:
    For a production fraud detection system, where the cost of missing a fraudulent transaction (a false negative) is far greater than the cost of investigating a false alarm (a false positive), the **GMM Oversampling model is the recommended approach**. Its superior ability to capture fraudulent activity (highest Recall) makes it the most effective model for minimizing financial losses, despite the operational need to review more flagged transactions.

-----
