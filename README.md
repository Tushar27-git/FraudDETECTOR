<div align="center">

# 🛡️ FraudDETECTOR — Real-Time ML Transaction Fraud Detection System

### *High-Throughput Machine Learning Engine for Financial Fraud Prevention & Behavioral Threat Intelligence*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100%2B-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![XGBoost](https://img.shields.io/badge/Model-XGBoost_Classifier-FF6600?style=for-the-badge&logo=xgboost&logoColor=white)](https://xgboost.readthedocs.io/)
[![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-1.3%2B-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![ROC-AUC](https://img.shields.io/badge/Performance-0.95%2B_ROC--AUC-success?style=for-the-badge)](https://github.com/Tushar27-git/FraudDETECTOR)

<p align="center">
  <strong>An end-to-end intelligent fraud prevention microservice powered by Gradient-Boosted Decision Trees (XGBoost), biometric behavioral telemetry, Explainable AI (XAI), and graph-based syndicate ring detection.</strong>
</p>

---

</div>

## 🌌 Overview

**FraudDETECTOR** is a high-performance transaction scoring and fraud intelligence platform designed for fintech applications, payment gateways, and banking security backends. The system analyzes incoming transaction payloads, transforms raw inputs into a 30-dimensional engineered feature vector, and computes instantaneous fraud probabilities through a pre-trained **XGBoost** model with sub-15ms latency.

Beyond standard tabular classification, FraudDETECTOR incorporates **multi-layered behavioral heuristics** (keystroke dynamics, cursor velocity, checkout dwell times) and **graph-based device correlation** to detect bot injections, credential stuffing, and organized fraud rings.

---

## 🎯 Key Features

- ⚡ **Real-Time FastAPI Backend**: Fully asynchronous, schema-validated REST API with CORS support for payment gateways, dashboards, and browser extensions.
- 🧠 **Optimized XGBoost ML Engine**: Trained on large-scale financial transaction benchmarks (~590k transactions) achieving **> 0.95 ROC-AUC** with temporal `GroupKFold` cross-validation.
- 🔍 **Explainable AI (XAI)**: Provides human-interpretable feature contribution breakdowns explaining *why* a transaction was flagged (e.g., velocity anomalies, abnormal amounts, device anomalies).
- 🖱️ **Biometric Behavioral Profiling**: Detects automated bot checkouts and script injections via typing cadence (CPM), mouse velocity (px/s), and interaction dwell times.
- 🕸️ **Fraud Ring & Syndicate Detection**: In-memory graph tracker identifying coordinated attacks across shared devices, locations, and high-frequency clusters.
- 🚦 **Tri-Tiered Risk Classification**: Categorizes transactions into `LOW`, `MEDIUM`, and `HIGH` risk profiles with calibrated probability scoring.

---

## 🏗️ System Architecture & Data Flow

```
                                  [ Transaction Request ]
                          (Amount, Card, Location, Device, Biometrics)
                                             │
                                             ▼
                                  [ FastAPI /predict ]
                                (Pydantic Schema Validator)
                                             │
                                             ▼
                                [ Feature Transformer ]
                      (Maps inputs into 30-dim Model Feature Vector)
                                             │
                       ┌─────────────────────┴─────────────────────┐
                       ▼                                           ▼
             [ XGBoost Inference ]                    [ Behavioral Biometrics ]
         (Base Probabilities via Trees)             (Typing Speed & Mouse Motion)
                       │                                           │
                       └─────────────────────┬─────────────────────┘
                                             │
                                             ▼
                                 [ Graph Syndicate Engine ]
                              (Device & IP Ring Correlation)
                                             │
                                             ▼
                               [ Risk Evaluator & XAI Engine ]
                             (Risk Tier + Feature Attribution)
                                             │
                                             ▼
                                 [ JSON Response Payload ]
                          { Probability, Risk Level, XAI, Graph }
```

---

## 🧪 Machine Learning Pipeline

### 1. 📊 Top Feature Selection (30-Dimension Optimization)
The model extracts the 30 most predictive features identified during extensive cross-validation on the IEEE-CIS benchmark dataset:

| Category | Features | Description |
| :--- | :--- | :--- |
| **Transaction Dynamics** | `V257`, `V258`, `V188`, `V70`, `V294`, `V156`, `V187`, `V283`, `V91`, `V142`, `V30`, `V162`, `V62`, `V289`, `V281` | V-Features capturing cross-transaction payment behavior and card velocities |
| **Transaction Counts** | `C1`, `C4`, `C5`, `C7`, `C8`, `C10`, `C11`, `C13`, `C14` | Frequency count metrics across card, address, and device combinations |
| **Card & Location** | `card3`, `card6`, `addr2`, `card3_FE` | Card type, regional billing addresses, and in-fold frequency encodings |
| **Identity & Matching** | `id_17`, `M4` | Device hardware identifiers and transaction match attributes |

### 2. 🛡️ Leakage-Free Temporal Validation
To accurately simulate real-world production performance, model training utilizes **GroupKFold (6 Splits)** grouped by transaction month (`DT_M`). Frequency encodings (`card3_FE`) are computed strictly within training folds, preventing future data leakage into validation subsets.

---

## 🔌 API Reference

### `GET /health`
Verifies microservice operational status and model readiness.

```json
{
  "status": "online",
  "model_loaded": true,
  "version": "2.0"
}
```

---

### `POST /predict`
Evaluates transaction parameters and returns risk scores, XAI explanations, and syndicate graph telemetry.

#### 📤 Request Body
```json
{
  "transaction_amount": 2450.00,
  "card_type": "visa",
  "user_location": "US",
  "transaction_frequency": 6,
  "device_type": "desktop",
  "mouse_speed_px_s": 920.5,
  "typing_speed_cpm": 580.0,
  "time_on_page_s": 1.2
}
```

#### 📥 Response Payload
```json
{
  "fraud_probability": 0.8842,
  "is_fraud": 1,
  "risk_level": "HIGH",
  "xai_explanations": [
    {
      "feature": "High Transaction Amount",
      "impact": "+20%",
      "type": "danger"
    },
    {
      "feature": "High Transaction Frequency",
      "impact": "+15%",
      "type": "danger"
    },
    {
      "feature": "Unnaturally Fast Checkout (Bot Behavior)",
      "impact": "+15%",
      "type": "danger"
    },
    {
      "feature": "Impossible Typing Speed (Pasted/Injected)",
      "impact": "+10%",
      "type": "danger"
    }
  ],
  "network_graph": {
    "node_count": 5,
    "connected_siblings": 4,
    "ring_detected": true
  }
}
```

---

## 🛠️ Tech Stack

- **Backend Framework**: [FastAPI](https://fastapi.tiangolo.com/) + [Uvicorn](https://www.uvicorn.org/) + [Pydantic v2](https://docs.pydantic.dev/)
- **Machine Learning**: [XGBoost](https://xgboost.readthedocs.io/) + [Scikit-Learn](https://scikit-learn.org/)
- **Data Engineering**: [Pandas](https://pandas.pydata.org/) + [NumPy](https://numpy.org/)
- **Model Serialization**: [Joblib](https://joblib.readthedocs.io/)
- **Architecture**: Modular Python Pipeline (`FeatureTransformer` -> `InferenceEngine` -> `REST API`)

---

## 🗂️ Project Structure

```
FraudDETECTOR/
├── main.py                     # FastAPI server & route handlers
├── inference.py                # Pipeline orchestration & XAI / graph logic
├── utils.py                    # FeatureTransformer & mathematical interactions
├── train_simplified_xgb.py     # Production XGBoost training script
├── exported.py                 # Full exploratory EDA & feature extraction pipeline
├── model/                      # Trained artifacts (generated upon training)
│   ├── fraud_model.pkl         # Serialized XGBoost model
│   └── preproc_artifacts.json  # Categorical & frequency mappings
├── sample_submission.csv       # Sample output predictions format
├── test_identity.csv           # Evaluation identity dataset
├── LICENSE                     # MIT License
└── README.md                   # Project documentation
```

---

## 🚀 Getting Started

### 📋 Prerequisites
- **Python**: `3.10` or higher
- **pip** package manager

### 1. Clone the Repository
```bash
git clone https://github.com/Tushar27-git/FraudDETECTOR.git
cd FraudDETECTOR
```

### 2. Set Up Virtual Environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux / macOS
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install fastapi uvicorn pydantic scikit-learn xgboost pandas numpy joblib matplotlib
```

### 4. Train the Model (Optional if pre-generated `model/` exists)
```bash
python train_simplified_xgb.py
```
*This generates `model/fraud_model.pkl` and `model/preproc_artifacts.json`.*

### 5. Launch the FastAPI Server
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

Access the interactive Swagger API documentation at: **[http://localhost:8000/docs](http://localhost:8000/docs)**

---

## 👥 Authors & Acknowledgements

- **Tushar** ([@Tushar27-git](https://github.com/Tushar27-git))
- **Kartik Singh Bhadoria** ([@Kartik-Singh-Bhadoria](https://github.com/Kartik-Singh-Bhadoria))
- **Shreya Mittal** ([@mittalshreya25-cpu](https://github.com/mittalshreya25-cpu))
- **Pranav** ([@Pranav2-4-7](https://github.com/Pranav2-4-7))

*Built with inspiration from real-world financial fraud intelligence architectures and IEEE-CIS benchmark methodologies.*

---

## 📄 License

This project is licensed under the **MIT License**. See the [`LICENSE`](LICENSE) file for details.