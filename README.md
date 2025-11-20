# ML + AWS Template (Semi-Complex)

This repository provides a **semi-complex machine learning project** wired for local training and scalable deployment on **AWS (S3 + Lambda + API Gateway)**.  
It is designed as a clean, extensible foundation for real-world ML workflows.

---

## 🚀 Features

### **Local ML Training**
- Loads dataset from CSV  
- Performs train/test split  
- Builds preprocessing + RandomForest pipeline (scikit-learn)  
- Evaluates with classification report  
- Saves model artifacts locally  

### **AWS Integration**
- Uploads trained model + metadata to Amazon **S3**
- Lambda inference function auto-downloads the **latest model** on cold start  
- API Gateway exposes a `/predict` endpoint  
- Serverless & auto-scaling due to Lambda concurrency  

### **Infrastructure**
- AWS SAM template for deploying Lambda + API Gateway  
- IAM policy for S3 access  
- Clean separation of code:
  - `src/` for training  
  - `lambda_inference/` for inference  
  - `infrastructure/` for IaC  

---

## 📁 Repository Structure

```
ml-aws-template/
├─ README.md
├─ requirements.txt
├─ .env.example
├─ src/
│  ├─ __init__.py
│  ├─ config.py
│  ├─ aws_utils.py
│  ├─ data_loader.py
│  ├─ features.py
│  ├─ model.py
│  ├─ train.py
│  ├─ evaluate.py
│  ├─ cli.py
├─ lambda_inference/
│  ├─ app.py
│  ├─ model_utils.py
│  └─ requirements.txt
└─ infrastructure/
   ├─ sam-template.yaml
   └─ iam-policy.json
```

---

## ⚙️ Setup Instructions

### **1. Create a virtual environment**

```bash
python -m venv .venv
source .venv/bin/activate          # macOS / Linux
# OR
.venv\Scriptsctivate             # Windows
```

### **2. Install dependencies**

```bash
pip install -r requirements.txt
```

### **3. Create and populate `.env`**

```bash
cp .env.example .env
```

Fill in real AWS credentials and S3 bucket names.

---

## 🧪 Local Training

```bash
python -m src.cli train-local data/my_dataset.csv
```

The training script will:

- Load CSV  
- Build preprocessing + RandomForest pipeline  
- Train model  
- Evaluate on a held-out test set  
- Save the trained model locally  
- Upload the model & metadata to S3  

---

## 📦 S3 Artifact Layout

Uploaded artifacts follow this pattern:

```
models/
 ├─ <model_name>_<timestamp>.pkl
 └─ <model_name>_<timestamp>_meta.json
```

Each metadata JSON includes:

- `model_name`
- Train/test row counts  
- Runtime metrics  
- Class labels  
- Weighted avg precision/recall/F1  

---

## ☁️ Deploying to AWS (Lambda + API Gateway)

### **1. Build SAM project**

```bash
sam build --template-file infrastructure/sam-template.yaml
```

### **2. Deploy**

```bash
sam deploy --guided
```

SAM will prompt for parameters and generate:

- Lambda function  
- IAM role  
- API Gateway endpoint  

At the end, it prints something like:

```
ApiUrl: https://xxxxx.execute-api.us-east-1.amazonaws.com/Prod/predict
```

---

## 🔮 Using the Prediction API

### **POST /predict**

**Example request body:**

```json
{
  "records": [
    {"feature1": 3.14, "feature2": "foo"},
    {"feature1": 1.23, "feature2": "bar"}
  ]
}
```

**Example response:**

```json
{
  "predictions": [0.82, 0.13],
  "count": 2
}
```

---

## 🏗️ Technology Stack

- **Python 3.11**  
- **scikit-learn** for ML  
- **pandas / numpy** for data prep  
- **boto3** for AWS interaction  
- **AWS Lambda** for inference  
- **S3** for artifact storage  
- **SAM** for infrastructure  

---

## 📌 Notes

- API keys in code samples are placeholders.  
- Replace them with real secrets using environment variables or AWS Secrets Manager.  
- For production, consider packaging shared code into a Lambda Layer.  

---

## 📜 License

MIT — feel free to use this template for commercial or personal projects.

