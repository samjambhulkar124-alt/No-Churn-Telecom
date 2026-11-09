# No-Churn Telecom — Customer Churn Prediction

A machine-learning project that investigates customer churn for a telecom operator, compares classification models, and evaluates whether customer attributes can support churn-risk scoring and retention campaigns.

> **Important result:** The available features did not produce useful churn predictions. The tuned LightGBM model achieved a test ROC-AUC of approximately **0.4997**, and its 95% bootstrap confidence interval (**0.4939–0.5066**) includes 0.50. This is effectively random-ranking performance. The risk scores and `CHURN-FLAG` are demonstrated as a workflow, **not as a model ready for real customer targeting**.

## Project objectives

1. Explore customer attributes and identify possible churn patterns.
2. Prepare customer data for machine-learning experiments.
3. Compare multiple classification algorithms using appropriate metrics.
4. Tune selected models and evaluate them on unseen test data.
5. Demonstrate churn-risk scores and a YES/NO `CHURN-FLAG`, while documenting their limitations.

## Repository structure

```text
No-Churn-Telecom/
├── README.md
├── No_Churn_Telecom.ipynb
├── requirements.txt
├── .gitignore
├── data/
│   └── README.md
└── outputs/
    └── sample_results.png
```

## Dataset

The original notebook reads data from a MySQL database:

- Database: `project_telecom`
- Table: `telecom_churn_data`
- Rows: 243,553
- Original columns: 14
- Target: `churn` (`0` = stayed, `1` = churned)

The dataset is **not included** in this repository. See [`data/README.md`](data/README.md) for access and privacy notes. Do not upload customer-level data or database credentials to a public repository.

## Workflow

- Load data from MySQL using SQLAlchemy and PyMySQL.
- Check missing values, duplicates, target distribution, ranges and data types.
- Replace negative values in `calls_made`, `sms_sent` and `data_used` with zero, as specified in the notebook.
- Drop `customer_id` and `pincode` as identifier-like fields.
- Convert `date_of_registration` to `tenure_days`.
- Perform exploratory analysis and one-hot encode categorical variables.
- Use a stratified 80/20 train/test split.
- Compare a Dummy baseline, Logistic Regression, Gaussian Naive Bayes, Decision Tree, Random Forest, HistGradientBoosting, LightGBM and XGBoost.
- Perform 5-fold stratified cross-validation and hyperparameter tuning.
- Evaluate the tuned LightGBM model using ROC-AUC, precision, recall, F1-score, a confusion matrix, ROC curve and a bootstrap confidence interval.
- Demonstrate risk scores, risk levels and `CHURN-FLAG` values.

## Key findings

| Evaluation | Notebook result |
|---|---:|
| Customers | 243,553 |
| Churned customers | 48,827 (about 20%) |
| Stayed customers | 194,726 (about 80%) |
| Best untuned 5-fold CV ROC-AUC | LightGBM: 0.5061 |
| Tuned LightGBM 3-fold search ROC-AUC | 0.5074 |
| Tuned LightGBM test ROC-AUC | 0.4997 |
| 95% bootstrap interval for test ROC-AUC | 0.4939–0.5066 |
| Artificial-signal pipeline sanity check | ROC-AUC about 0.76 |

The very small cross-validation improvement after tuning does not translate into useful held-out test performance. The confidence interval includes 0.5, and observed churn rates across predicted-risk groups remain close to the overall churn rate.

![Model evaluation results](outputs/sample_results.png)

## Requirements

- Python 3.10 or newer recommended
- A MySQL database containing the authorized project dataset
- Python packages listed in [`requirements.txt`](requirements.txt)

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/No-Churn-Telecom.git
cd No-Churn-Telecom
```

### 2. Create and activate a virtual environment

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

macOS / Linux:

```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure database environment variables

Set the following variables in your terminal or IDE environment. Use credentials supplied by the database owner.

Windows PowerShell:

```powershell
$env:MYSQL_HOST="your-database-host"
$env:MYSQL_USER="your-database-user"
$env:MYSQL_DATABASE="project_telecom"
$env:MYSQL_PASSWORD="your-password"
```

macOS / Linux:

```bash
export MYSQL_HOST="your-database-host"
export MYSQL_USER="your-database-user"
export MYSQL_DATABASE="project_telecom"
export MYSQL_PASSWORD="your-password"
```

Do not commit passwords, `.env` files, private connection strings or customer data.

### 5. Run the notebook

```bash
jupyter notebook No_Churn_Telecom.ipynb
```

Run cells from top to bottom. The notebook expects the MySQL table `telecom_churn_data` to be accessible to the configured account. It creates local CSV outputs during execution; these are excluded from Git by `.gitignore`.

## Interpretation and limitations

- The target is imbalanced, so accuracy alone is not a sufficient evaluation metric.
- The strongest absolute feature correlation reported in the notebook is only about 0.0052.
- The tuned model's test ROC-AUC is approximately 0.50, which indicates no meaningful ranking ability on this dataset.
- A threshold that maximizes F1 can still be operationally useless; the notebook's selected threshold flags essentially everyone.
- The `CHURN-FLAG` and campaign list are demonstrations only. Do not use them to contact or prioritize real customers based on this model.
- More informative and properly authorized features may be needed, such as billing history, plan changes, complaint history, support interactions and network-quality indicators.
- Any future production model should be validated on fresh data, checked for leakage and bias, monitored over time, and tested through a controlled retention pilot.

## Responsible data use

Only use data you are authorized to access. Remove or anonymize personal information before sharing examples. Follow applicable privacy, security and organizational requirements. This repository intentionally does not contain the source dataset or customer-level prediction exports.

## Author

Add your name, contact details and relevant profile links here before publishing.
