# Predictive-Banking-Marketing-Intelligence

# 🏦 Predictive Banking Marketing Intelligence

### Predicting Customer Response to Improve Marketing Resource Allocation

This project develops an end-to-end machine learning solution to help a bank prioritize marketing contacts for term-deposit campaigns.

Instead of contacting customers broadly, the solution uses historical customer, campaign, and economic information to estimate subscription potential, rank contacts, and identify a practical targeting strategy.

The final model-based strategy shows that contacting the **Top 20% of ranked contacts** captures approximately **66% of historical subscribers** in the evaluation data while avoiding approximately **6,589 calls** compared with contacting the full evaluation set.

---

## 🎯 Business Problem

Bank marketing campaigns require significant time and resources, but only about **11.27% of contacts** in the cleaned historical dataset resulted in a term-deposit subscription.

The key business question is:

> **How can historical customer and campaign information be used to identify who should be contacted first?**

The project focuses not only on prediction, but also on translating model results into a practical contact-prioritization strategy.

---

## 💡 Solution Overview

The project follows an end-to-end analytics and machine learning workflow:

**Historical Data → Data Preparation → EDA → Customer Segmentation → Predictive Modeling → Customer Ranking → Lift & Gains Analysis → Resource Allocation → Business Dashboard**

The solution combines:

- Exploratory Data Analysis
- Feature Engineering
- Customer Segmentation
- Supervised Machine Learning
- Hyperparameter Tuning
- Customer Priority Ranking
- Lift and Cumulative Gains Analysis
- Resource Allocation Analysis
- Interactive Business Dashboard

---

## 📊 Dataset

The project uses the **UCI Bank Marketing Dataset**, which contains information from direct marketing campaigns conducted by a Portuguese banking institution.

### Dataset Summary

| Metric | Value |
|---|---:|
| Original Records | 41,188 |
| Records After Duplicate Removal | 41,176 |
| Subscription Rate | 11.27% |
| Non-Subscription Rate | 88.73% |
| Target Variable | `y` |
| Target Classes | Yes / No |

Each row represents a marketing contact rather than necessarily representing a unique customer.

### Main Data Categories

The dataset includes:

- Customer demographics
- Financial characteristics
- Current campaign information
- Previous campaign history
- Contact timing
- Economic indicators
- Term-deposit subscription outcome

---

## 🔍 Data Preparation & Feature Engineering

Several preprocessing decisions were made to create a modeling-ready dataset.

**Key Strategies**

- Removed exact duplicate records
- Preserved meaningful `"unknown"` categories
- Converted `pdays = 999` into a more interpretable previous-contact representation
- Created `previously_contacted`
- Created `days_since_previous_contact`
- Created customer `age_group`
- Created `campaign_capped` to reduce the influence of extreme campaign-contact values
- Encoded categorical variables
- Standardized numerical variables where appropriate

**Leakage Prevention**

The variable `duration` was excluded from predictive modeling.

Although call duration is highly associated with subscription, it is only known **after the marketing call occurs**. Since the goal is to decide who should be contacted **before making the call**, including this variable would introduce data leakage.

---

## 📂 Project Structure

```
Predictive-Banking-Marketing-Intelligence/
│
├── data/
│   └── bank-additional-full.csv
│
├── notebooks/
│   └── ADS505_Final_Project.ipynb
│
├── dashboard/
│   ├── app.py
│   ├── contact_depth_analysis.csv
│   ├── customer_priority_dashboard.csv
│   └── feature_importance.csv
│
├── README.md
├── requirements.txt
└── presentation/
```

### 🔄 Reproducibility

```
git clone <repository-url>
```
```
cd Predictive-Banking-Marketing-Intelligence
```
```
pip install -r requirements.txt
```

Run the project notebook to reproduce:
1. Data preparation
2. Exploratory analysis
3. Feature engineering
4. Customer segmentation
5. Model training and tuning
6. Model evaluation
7. Customer ranking
8. Lift and cumulative gains analysis
9. Dashboard output files

    
Then launch the Streamlit dashboard:

```
python -m streamlit run dashboard/app.py
```
---

## 📈 Exploratory Data Analysis

EDA was used to understand:

- Subscription behavior
- Customer characteristics
- Financial variables
- Campaign characteristics
- Previous campaign outcomes
- Economic conditions
- Relationships among predictors

One important pattern was the relationship between previous campaign outcomes and future subscription behavior.

| Previous Campaign Outcome | Historical Subscription Rate |
|---|---:|
| Success | ~65% |
| Failure | ~14% |
| No Previous Campaign | ~9% |
| Overall | ~11% |

Previous campaign history therefore provides useful predictive information for prioritizing future contacts.

---

## 👥 Customer Segmentation

K-Means clustering was used as an exploratory technique to identify groups with similar customer and previous-campaign characteristics.

The target variable was **not used during clustering**.

**Cluster Selection**

Possible values of `k = 2–8` were evaluated using:

- Elbow Method
- Silhouette Score

A two-cluster solution provided the highest silhouette score among the evaluated values.

### Cluster Profiles

| Cluster | Share of Records | Subscription Rate | Main Pattern |
|---|---:|---:|---|
| Cluster 0 | 86.34% | 8.83% | Primarily no previous campaign history |
| Cluster 1 | 13.66% | 26.65% | Previous campaign activity |

The segmentation primarily reflects differences in **previous campaign history**, showing that prior interactions are an important characteristic separating the groups.

Clustering is used here as an exploratory and descriptive tool rather than as the final prediction method.

---

## 🤖 Predictive Modeling

The supervised learning objective is to estimate the likelihood of term-deposit subscription before a marketing call is made.

### Models Evaluated

Four classification models were compared:

- Logistic Regression
- Decision Tree
- Random Forest
- XGBoost

The data was divided using an **80/20 stratified train-test split**.

Rather than creating a separate fixed validation dataset, **5-fold stratified cross-validation** was performed within the training data for model comparison and hyperparameter tuning. The 20% test set remained untouched until final evaluation.

### Primary Evaluation Metric

Because only approximately **11% of contacts subscribed**, the target is imbalanced.

Therefore, **PR-AUC (Precision-Recall Area Under the Curve)** was used as the primary model-comparison metric.

PR-AUC is useful because it evaluates the balance between:

- **Precision:** How many predicted subscribers actually subscribed?
- **Recall:** How many actual subscribers did the model identify?

---

## 🏆 Model Results

### Cross-Validation Performance

| Model | CV PR-AUC |
|---|---:|
| Logistic Regression | 0.4500 |
| Decision Tree | 0.4207 |
| Random Forest | 0.4684 |
| XGBoost | **0.4697** |

Random Forest and XGBoost showed very similar performance.

### Holdout Test Performance

| Model | ROC-AUC | PR-AUC |
|---|---:|---:|
| Logistic Regression | 0.8003 | 0.4540 |
| Decision Tree | 0.8038 | 0.4441 |
| **Random Forest** | **0.8127** | **0.4902** |
| XGBoost | 0.8126 | 0.4847 |

Random Forest was retained for the final customer-ranking and business analysis because it provided strong holdout ranking performance, slightly higher test PR-AUC, and interpretable feature importance.

---

## 🎯 Customer Ranking Strategy

Rather than relying only on a yes/no classification, the final model was used to **rank contacts from higher to lower predicted subscription potential**.

This allows the bank to decide how deeply it wants to work through the ranked contact list.

### Contact-Depth Analysis

| Contact Depth | Contacts | Subscribers Captured | Capture Rate | Lift |
|---:|---:|---:|---:|---:|
| 10% | 823 | 434 | 46.77% | 4.68× |
| **20%** | **1,647** | **614** | **66.16%** | **3.31×** |
| 30% | 2,470 | 689 | 74.25% | 2.48× |
| 50% | 4,118 | 779 | 83.94% | 1.68× |
| 100% | 8,236 | 928 | 100% | 1.00× |

---

## 📈 Lift & Cumulative Gains

Lift analysis compares model-based targeting with random targeting.

At the **Top 20% contact depth**:

- **1,647** contacts are prioritized
- **614 of 928** historical subscribers are captured
- **66.16%** of subscribers are reached
- **3.31× lift** is achieved over random targeting
- Approximately **6,589 calls are avoided** compared with contacting the full evaluation population

Increasing the contact depth from **20% to 30%** requires approximately **823 additional calls** while historically capturing only **75 additional subscribers**.

This demonstrates diminishing returns as the bank moves further down the ranked contact list.

---

## 💼 Resource Allocation Recommendation

### Recommended Starting Strategy: Top 20%

The analysis recommends the **Top 20% of model-ranked contacts as a practical starting point**.

This strategy:

- Concentrates potential subscribers near the top of the contact list
- Captures approximately **66% of historical subscribers**
- Requires only **20% of the evaluation calls**
- Produces approximately **3.31× lift**
- Avoids approximately **80% of calls** compared with contacting everyone

> **Important:** The Top 20% should be interpreted as a practical, resource-efficient starting point rather than a financially optimal cutoff.

Actual campaign call costs and the financial value of a successful term-deposit subscription were not available in the dataset.

---

## 🔎 Model Explainability

Random Forest feature importance indicates that predictions rely on a combination of:

- Economic conditions
- Previous campaign history
- Previous contact information
- Customer characteristics
- Current campaign characteristics

Important predictors included:

- `euribor3m`
- `nr.employed`
- `days_since_previous_contact`
- `previously_contacted`
- `emp.var.rate`
- `cons.conf.idx`
- `poutcome_success`
- `age`

Feature importance represents **predictive importance and should not be interpreted as causal impact**.

---
## 🖥️  Interactive Business Dashboard

A Streamlit dashboard translates the machine learning results into a business decision-support tool.

https://predictive-banking-marketing-intelligence.streamlit.app/

Users can:

- Select the percentage of contacts to target
- View the number of prioritized contacts
- See historical subscribers captured
- Compare targeting performance with random selection
- View calls avoided
- Explore lift at different contact depths
- Review important predictive features
- View a ranked customer/contact priority list
- Adjust illustrative cost and subscription-value assumptions

---
## 💰 Business Value
The project moves beyond simply asking:
“Will this contact subscribe?”

and instead asks:
“Who should the bank contact first?”

**Model-based prioritization can help marketing teams:**
- Reduce unnecessary calls
- Focus resources on higher-potential contacts
- Reach a large share of potential subscribers with fewer calls
- Understand the trade-off between campaign effort and subscriber capture
- Make targeting decisions using measurable lift and gains
---
  
## ⚠️ Limitations
Several limitations should be considered:
- The dataset represents historical campaign behavior.
- Rows represent contacts and may not represent unique customers.
- Actual call costs were not available.
- Actual financial value per successful subscription was not available.
- Economic variables can change over time.
- Feature importance indicates predictive association rather than causation.
- The Top 20% strategy has not yet been validated through a live marketing experiment.

---

## 🚀  Future Work
1. A/B Testing
Test model-based targeting against the bank's current targeting approach in a controlled campaign.
2. Time-Based Validation & Monitoring
Train on earlier campaigns and evaluate on later campaigns to determine whether model performance remains stable as customer behavior and economic conditions change.
3. Financial Optimization
Incorporate actual:
- Cost per marketing call
- Revenue or profit per successful subscription
- Campaign capacity constraints
This would allow the bank to identify a financially optimal contact depth rather than relying only on lift and subscriber capture.
---

### 🛠️ Technologies Used
- Python
- Pandas
- NumPy
- Scikit-learn
- XGBoost
- Matplotlib
- Plotly
- Streamlit
- Jupyter Notebook
- Git / GitHub

  ## Project Team

  - Yubi Joy Quinzon
  - Mehson Delan
  - Sheshma Jaganathan
