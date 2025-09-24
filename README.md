# Credit Card FraudDetection

## Introducing

This project aims to detect fraudulent credit card transactions in a highly imbalanced dataset. Dataset taken from: (https://www.kaggle.com/datasets/kartik2112/fraud-detection/data)).

- Kaggle Perfomance: https://www.kaggle.com/code/auburn0306/fraud-detection-model
  
| Model           | Precision (0) | Recall (0) | Precision (1) | 🔴Recall (1) | ROC-AUC | PR-AUC |
|-----------------|---------------|------------|---------------|------------|---------|--------|
| LogisticReg     | 0.999         | 0.931      | 0.021         | *0.701*      | 0.894   | 0.155  |
| RandomForest    | 0.999         | 0.999      | 0.720         | *0.817*     | 0.993   | 0.837  |
| XGBoost         | 0.999         | 0.999      | 0.810         | *0.810*      | 0.997   | 0.873  |
| LightGBM Auto   | 1.000         | 0.995      | 0.438         | *0.924*  | 0.998   | 0.888  |
| LightGBM Manual | 1.000         | 0.998      | 0.593         | *0.900*      | 0.998   | 0.892 |

- Objective:

In fraud detection, achieving a strong balance between precision and recall remains a critical difficulty. Most current models, including top-performing Kaggle solutions, often optimize overall metrics but still struggle with recall. A large part of existing work relies on oversampling or undersampling, yet these methods do not fully resolve the problem and may even limit model efficiency. What remains unclear and forms the core of my challenge is whether future progress in fraud detection will come more from advanced feature engineering or from improved sampling techniques

## Pipelines
- Data Preprocessing:
  
      - Descriptive Analysis
  
      - Handling Nulls and Duplicate Values
  
      - Exploratory data analysis
  
- Feature Engineering:
  
      - **Time Series Features**: Periodic variable using von Mises distribution, vm_anomaly, vm_distance, vm_pdf, theta
  
      - **Customer Behaviors Features**: Time since the last transaction, Previous amount of the transaction
  
      (Both Time Series and Customer Behaviors are used based on the study by Alejandro Correa Bahnsen et al on Feature Engineering      strategies for credit card fraud detection)
  
      - Customer Information: distance_merch, age, clean_street_name, clean_merchant
  
      - Encode: Ordinal Encode for Categorical Features
  
      - Normalization: Z-score scale for entire dataset 1.2 mil records and 27 features

- Imbalance Dataset:
  
      - Undersampling NearMiss version 1, Random Under Samping (majority)
  
      - Applied SMOTE oversampling to generate synthetic fraud cases

- Modeling & Evaluation:
  
      - Models compared: Logistic Regression, Decision Tree, Random Forest, XGBoost
  
      - Metrics: Precision, Recall, F1, ROC-AUC and PR-AUC (especially preferred over accuracy for imbalanced data)

## Evaluation:

- Comparison :
  
| Model                       | Accuracy | Precision (class 1) | 🔴 Recall (class 1) | F1 (class 1) | ROC AUC | PR AUC |
|-----------------------------|----------|----------------------|------------------|--------------|---------|--------|
| Logistic Regression_nearmiss | 0.7709   | 0.0144              | 0.8681           | 0.0284       | 0.8960  | 0.1400 |
| Logistic Regression_raw      | 0.9485   | 0.0600              | 0.8410           | 0.1119       | 0.9411  | 0.2054 |
| Logistic Regression_rud      | 0.9501   | 0.0619              | 0.8424           | 0.1153       | 0.9403  | 0.2051 |
| Logistic Regression_SMOTE    | 0.9441   | 0.0553              | 0.8387           | 0.1038       | 0.9412  | 0.2072 |
| Random Forest_nearmiss       | 0.5438   | 0.0082              | 0.9772           | 0.0163       | 0.9672  | 0.6045 |
| Random Forest_rud            | 0.9801   | 0.1578              | 0.9608           | 0.2711       | 0.9946  | 0.8676 |
| XGBoost_nearmiss             | 0.7804   | 0.0171              |🟢0.9883         | 0.0337       | 0.9830  | 0.5199 |
| XGBoost_raw                  |🟢0.999   | 🟢0.8167           | 0.9366           | 🟢0.8725    | 🟢0.9990| 🟢0.9572|
| XGBoost_rud                  | 0.9862   | 0.2160              | 0.9748           | 0.3537       | 0.9984  | 0.9023 |
| XGBoost_SMOTE                | 0.9968   | 0.5504              | 0.9394           | 0.6901       | 0.9966  | 0.9292 |
| Decision Tree_nearmiss       | 0.6865   | 0.0118              | 0.9770           | 0.0233       | 0.8279  | 0.0116 |
| Decision Tree_rud            | 0.9616   | 0.0871              | 0.9459           | 0.1596       | 0.9538  | 0.0826 |

- Results
  
      - Accuracy: highest is XGBoost_raw (0.999), but accuracy is misleading in imbalanced datasets and not important in Fraud Detection.
  
      - Precision (class 1): XGBoost_raw (0.8167) is way higher than others → means fewer false positives.

      - Recall (class 1): XGBoost_nearmiss (0.9883) is the best → means it catches almost all fraud cases.

      - F1 (class 1): XGBoost_raw (0.8725) is the best balance between precision and recall.

      - ROC AUC: XGBoost_raw (0.999) is highest.

      - PR AUC: XGBoost_raw (0.9572) is highest.

- Best overall model:

XGBoost_raw → because it has strong precision, recall, F1, ROC AUC, PR AUC all at the top.

⚖️ But…

If care more about catching fraud (recall), then XGBoost_nearmiss is slightly better (recall ~0.99).

If care about balance (precision + recall), then XGBoost_raw is the winner.

👉 In most fraud detection tasks, recall is often prioritized (don’t miss fraud), but with recall already at ~0.94 for XGBoost_raw and much better precision, I’d still go with XGBoost_raw as the best trade-off.

- Feature Importance of XG_Boost Raw:
  
| Rank | Feature                       | Importance   |
|------|-------------------------------|--------------|
| 1    | amt                           | 2577.105957  |
| 2    | previous_amount               | 1566.873291  |
| 3    | hour                          | 924.216858   |
| 4    | category                      | 480.331116   |
| 5    | theta                         | 333.987793   |
| 6    | time_since_last_transaction   | 228.627609   |
| 7    | vm_sigma                      | 165.127472   |
| 8    | vm_distance                   | 109.298470   |
| 9    | vm_pdf                        | 108.981255   |
| 10   | age                           | 102.310539   |
| 11   | vm_mu                         | 99.364159    |
| 12   | gender                        | 81.257225    |
| 13   | city_pop                      | 49.978405    |
| 14   | long                          | 38.590202    |
| 15   | state                         | 36.201256    |
| 16   | lat                           | 32.657249    |
| 17   | street                        | 27.597160    |
| 18   | distance_merch                | 27.478428    |
| 19   | merch_long                    | 27.060026    |
| 20   | job                           | 26.662313    |

## Conclustion: 

The results show that the most influential features come from two groups:

- Time Series Features: periodic variables modeled with the von Mises distribution, such as vm_anomaly, vm_distance, vm_pdf, theta.

- Customer Behavior Features: variables capturing transaction dynamics, such as time since the last transaction and previous transaction amount.

These features—originally highlighted in the study by Alejandro Correa Bahnsen et al. on feature engineering for credit card fraud detection—proved to be the decisive factors in achieving the highest model performance. Importantly, these types of features have not been widely adopted in existing Kaggle projects. This demonstrates that feature engineering, rather than sampling methods alone, is the most critical driver of success in fraud detection models.

