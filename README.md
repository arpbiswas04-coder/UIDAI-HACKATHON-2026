### 🆔 UIDAI Aadhaar Anomaly & Fraud Detection System (UIDAI Hackathon 2026)

An advanced Unsupervised Machine Learning system designed to identify fraudulent activities and operational anomalies in Aadhaar enrollment and update registries. This project aggregates geographical and temporal logs (biometrics, demographics, and enrollments) to pinpoint high-risk locations and suspicious centers using the **Isolation Forest** anomaly detection algorithm.

---

## 💡 The Problem & The Solution
Aadhaar registers millions of biometric updates, demographic changes, and new enrollments daily across thousands of centers in India. Traditional rule-based engines fail to detect sophisticated fraud patterns, such as:
1. **Suspicious Ratios**: Extremely high biometric updates without matching demographic updates.
2. **Age Anomalies**: Unusually high rates of child biometrics relative to adult updates in specific postal zones.
3. **Enrollment Spikes**: Sudden surges in new enrollments compared to standard historical averages.

This project solves this by merging multiple large-scale registries and training an **Isolation Forest** model to establish a multi-dimensional baseline of normal center behaviors. Any deviation is automatically flagged, scored, and categorized into Low, Medium, and High-Risk alerts.

---

## ✨ Features

- **📊 Multi-Registry Aggregation**: Ingests and aggregates half a million rows across three separate core Aadhaar registries:
  - **Biometric Logs** (`api_data_aadhar_biometric_...`)
  - **Demographic Logs** (`api_data_aadhar_demographic_...`)
  - **Enrollment Logs** (`api_data_aadhar_enrolment_...`)
- **🛠️ Feature Engineering & Ratio Extraction**: Computes statistical ratios critical for anomaly identification:
  - `bio_demo_ratio`: Biometric updates relative to demographic updates.
  - `child_bio_ratio`: Biometrics of children (ages 5–17) vs. adults (ages 17+).
  - `enrolment_update_ratio`: New enrollments compared to total update activities.
  - `biometric_enrolment_ratio`: Biometric updates vs. new enrollments.
- **🤖 Machine Learning Anomaly Detection**:
  - Leverages **Isolation Forest** (with 200 estimators and standard 2% contamination) to construct isolation trees for multidimensional outliers.
  - Standardizes data using **StandardScaler** to eliminate scale bias.
- **🚨 Risk Stratification**:
  - Automatically flags cases (`High`, `Medium`, `Low`) based on mathematical decision functions.
  - Exports a sorted list of suspicious regions to `fraud_alerts.csv` for immediate investigation by authorities.

---

## 🛠️ Technology Stack

- **Data Ingestion & Manipulation**: Pandas, NumPy
- **Machine Learning**: Scikit-Learn (IsolationForest, StandardScaler)
- **Visualization**: Matplotlib
- **Notebook & Modeling**: Jupyter Notebook (IPython)
- **Data Export Formats**: CSV, XLS (Microsoft Excel)

---

## 📂 Project Structure

```text
UIDAI-HACKATHON-2026/
├── final adhaar anomaly (1).ipynb   # Core Jupyter Notebook (Ingestion, EDA, training, risk scoring)
├── final_fraud_anomaly_output1.xls  # Excel report detailing flagged regions and anomaly scores
├── README.md                        # Project documentation
└── [Required Datasets - Ignored/External]:
    ├── api_data_aadhar_biometric_0_500000.csv
    ├── api_data_aadhar_enrolment_0_500000.csv
    └── api_data_aadhar_demographic_0_500000.csv
```

---

## ⚙️ How It Works (The Machine Learning Pipeline)

### 1. Ingestion & Spatial Merging
The pipeline ingests raw datasets partitioned by `date`, `state`, `district`, and `pincode`. The tables are aggregated using `.groupby().sum()` and merged using an `inner merge` to construct a unified geographical footprint of operational activity.

### 2. Activity Filtering (Quantile Masking)
To prevent sparse, low-activity rural pin codes from skewing anomaly metrics, the data is masked to keep only regions falling above the **70th percentile of total operations activity**.

### 3. Isolation Forest Modeling
An unsupervised forest is trained on 12 distinct scaled features:
```python
features = [
    "bio_age_5_17", "bio_age_17_", "demo_age_5_17", "demo_age_17_",
    "age_0_5", "age_5_17", "age_18_greater", "bio_demo_ratio",
    "child_bio_ratio", "enrolment_update_ratio", "biometric_enrolment_ratio",
    "total_activity"
]
```
The Isolation Forest calculates path lengths inside decision trees to isolate anomalies:
* **`fraud_flag = -1`**: Explicit anomaly (isolated quickly, indicating high structural differences).
* **`fraud_score`**: The closer the score is to negative, the higher the anomaly probability.

---

## 🚀 Quick Start & Installation

### Prerequisites
- Python 3.9 or higher.
- Jupyter Notebook or JupyterLab.

### 1. Clone the Repository
```bash
git clone https://github.com/arpbiswas04-coder/UIDAI-HACKATHON-2026.git
cd UIDAI-HACKATHON-2026
```

### 2. Create and Activate Virtual Environment
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows, use: .venv\Scripts\activate
```

### 3. Install Dependencies
```bash
pip install pandas numpy scikit-learn matplotlib jupyter openpyxl
```

### 4. Running the Notebook
Place your raw Aadhaar logs inside the repository folder, launch Jupyter:
```bash
jupyter notebook
```
Open `final adhaar anomaly (1).ipynb` and run all cells sequentially. The pipeline will output `fraud_alerts.csv` and detailed plots classifying anomalous districts.

---

## 📜 License
This project is open-source and developed for the UIDAI Hackathon 2026.
```
