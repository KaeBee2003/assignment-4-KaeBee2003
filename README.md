Of course\! Here is a comprehensive `README.md` file based on the provided Jupyter Notebook. You can copy and paste this directly into your `README.md` file on GitHub.

-----

# Credit Card Fraud Detection with Advanced Sampling Techniques

## Objective

This project aims to address the critical issue of class imbalance in a credit card fraud dataset. The primary goal is to build a robust fraud detection model by employing advanced sampling techniques, specifically using a **Gaussian Mixture Model (GMM)** for oversampling and a **hybrid approach combining GMM with Clustering-Based Undersampling (CBU)**. The performance of these methods is compared against a baseline Logistic Regression model trained on the original, imbalanced data.

-----

## Dataset

  - **Source:** `creditcard.csv`
  - **Description:** The dataset contains credit card transactions, with features that are the result of a PCA transformation. The only features that have not been transformed with PCA are 'Time' and 'Amount'. The target variable is 'Class'.
  - **Key Challenge: Severe Class Imbalance**
      - **Class 0 (Non-Fraudulent):** 284,315 samples (99.83%)
      - **Class 1 (Fraudulent):** 492 samples (0.17%)
      - **Imbalance Ratio:** The dataset has an extreme imbalance ratio of approximately **$578:1$**. This poses a significant challenge, as models trained on this data will be heavily biased towards the majority class, leading to poor detection of fraudulent transactions.

-----

## Methodology

The project is structured into three main parts, following a systematic approach from establishing a baseline to implementing and evaluating advanced solutions.

### Part A: Baseline Model & Data Analysis

1.  **Exploratory Data Analysis:** The initial analysis confirmed the severe class imbalance using bar and pie charts, quantifying the distribution of the two classes.
2.  **Data Splitting:** The data was split into training (70%) and testing (30%) sets using a stratified split to ensure that the proportion of fraudulent transactions was maintained in both sets.
3.  **Baseline Model:** A standard **Logistic Regression** model was trained on the original, imbalanced training data. This model serves as our baseline to measure the effectiveness of the sampling techniques.
      - **Performance:** While achieving a high accuracy of **99.91%**, the baseline model's performance on the minority (fraud) class was modest, with a **Recall of 70.95%**. This means it failed to identify nearly 30% of fraudulent transactions.

### Part B: Gaussian Mixture Model (GMM) for Synthetic Sampling

This part explores using GMMs as a generative approach to create synthetic data points for the minority class.

1.  **Theoretical Foundation (GMM vs. SMOTE):**

      - Unlike SMOTE, which **interpolates** between existing minority points, a GMM **generates** new samples by learning the underlying probability distribution of the data.
      - GMM is superior for complex data structures because it can model multi-modal distributions (data with distinct sub-clusters) and non-convex shapes, whereas SMOTE's linear interpolation might create unrealistic samples in these scenarios.

2.  **GMM Implementation:**

      - A GMM was trained exclusively on the minority (fraud) class data from the training set.
      - To find the optimal number of Gaussian components (`k`), the **Bayesian Information Criterion (BIC)** and **Akaike Information Criterion (AIC)** were calculated for a range of `k` values (1 to 59).
      - The BIC score showed a clear "elbow" and suggested an optimal `k` of **21**, which was chosen for the final model as it penalizes model complexity more heavily than AIC.

3.  **Model 2: GMM Oversampling:**

      - The trained GMM (with `k=21`) was used to generate new synthetic fraud samples.
      - The number of generated samples was calculated to perfectly balance the training set, making the count of fraudulent transactions equal to that of non-fraudulent ones (199,020 samples each).

4.  **Model 3: Hybrid Rebalancing (GMM + CBU):**

      - To create a more manageable yet balanced dataset, a hybrid strategy was implemented.
      - **Clustering-Based Undersampling (CBU):** The majority (non-fraud) class was undersampled using `KMeans`. The centroids of the clusters were used as representatives of the majority class, reducing its size from 199,020 to 688 samples.
      - **GMM Oversampling:** The GMM was then used to oversample the minority class to match the new, smaller majority class size, resulting in a balanced dataset with 688 samples for each class.

### Part C: Performance Evaluation and Conclusion

Two new Logistic Regression models were trained: one on the GMM-oversampled data (Model 2) and another on the GMM+CBU hybrid data (Model 3). Their performances were evaluated on the original, imbalanced test set.

1.  **Comparative Analysis:** The key performance metrics for detecting the minority (fraud) class were compared across all three models.

| Model                     | Precision | Recall   | F1-Score |
| ------------------------- | --------- | -------- | -------- |
| **Baseline (Imbalanced)** | 74.47%    | 70.95%   | 72.66%   |
| **GMM Oversampling** | 5.38%     | 87.16%   | 10.13%   |
| **GMM + CBU Hybrid** | 1.19%     | **91.89%** | 2.35%    |

2.  **Analysis of Results:**

      - Both GMM-based strategies significantly improved **Recall** compared to the baseline. This is crucial for fraud detection, as the primary goal is to identify as many fraudulent transactions as possible.
      - The improvement in Recall came at the cost of a drastic drop in **Precision**, leading to a higher number of false positives. This is a common trade-off in imbalanced classification problems.
      - The **GMM + CBU Hybrid** model achieved the highest Recall (**91.89%**), successfully identifying almost 92% of all fraudulent transactions in the test set.

3.  **Final Recommendation:**
    For a production fraud detection system, where the cost of missing a fraudulent transaction (a false negative) is significantly higher than the cost of investigating a false alarm (a false positive), the **GMM + CBU Hybrid model is the recommended approach**. Its superior ability to capture fraudulent activity (highest Recall) makes it the most effective model for minimizing financial losses, despite the operational overhead of reviewing more false positives.

-----
