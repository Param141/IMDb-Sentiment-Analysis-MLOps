# 🎬 IMDb Sentiment Analysis: Production MLOps Pipeline with EKS & Observability


---

```markdown
# 🎬 IMDb Sentiment Analysis: Production MLOps Pipeline with EKS & Observability

Welcome to the **IMDb Sentiment Analysis MLOps Capstone Project**. This repository demonstrates an enterprise-grade, end-to-end Machine Learning Operations system designed to classify movie review sentiments into positive or negative categories. 

The project goes beyond basic model training by establishing a complete production lifecycle: modular scaffolding via Cookiecutter, remote experiment tracking and model registry on **DagsHub / MLflow**, automated multi-stage pipeline orchestration and remote data versioning via **DVC & AWS S3**, containerized serving with **Flask**, continuous integration and deployment (**CI/CD**) with **GitHub Actions**, orchestrated deployment on **AWS EKS (Kubernetes)**, and real-time observability using **Prometheus & Grafana**.

---

## 🏗️ High-Level System Architecture

```text
[Raw Review Data (S3 / Local)] 
            │
            ▼
[DVC Pipeline Orchestration] ➔ [Data Ingestion ➔ Preprocessing ➔ Feature Engg (BoW / TF-IDF) ➔ Training]
            │
            ▼
[Experiment Tracking & Model Registry (MLflow & DagsHub)] ➔ [Automated Promotion: Staging ➔ Production]
            │
            ▼
[GitHub Actions CI/CD] ➔ [Unit/Model Tests] ➔ [Docker Container Build] ➔ [Push Image to AWS ECR]
                                                                                │
                                                                                ▼
[Live Observability] 🠄 [Metrics Scraped] 🠄 [Flask App Deployed on AWS EKS Cluster (2 Replicas)]
 (Prometheus & Grafana)

```

---

## 📁 Project Setup and Structure

### Step 1: Environment & Package Setup

Create an isolated Conda environment and install all dependencies:

```bash
conda create -n sentiment python=3.10 -y
conda activate sentiment
pip install -r requirements.txt

```

Install local packages in editable mode:

```bash
pip install -e .

```

Verify package installations:

```bash
pip list

```

### Step 2: Project Architecture

```text
├── .github/workflows/
│   └── ci.yaml                       # End-to-end CI/CD workflow definition
├── data/
│   ├── raw/                          # Ingested raw datasets (train.csv, test.csv)
│   ├── interim/                      # Cleaned and normalized text data
│   └── processed/                    # Vectorized matrices (BoW / TF-IDF)
├── flask_app/
│   ├── templates/index.html          # Frontend UI for live inference
│   ├── app.py                        # Serving app with Prometheus client metrics
│   └── requirements.txt              # Production-optimized Docker dependencies
├── models/                           # Serialized preprocessors and vectorizer artifacts
├── notebooks/                        # Modular experiment notebooks
├── reports/
│   └── metrics.json                  # Automated stage evaluation metrics
├── scripts/
│   └── promote_model.py              # Automated MLflow Staging-to-Production promoter
├── src/
│   ├── connections/
│   │   └── s3_connection.py          # Boto3 client handler for S3 bucket operations
│   ├── data/
│   │   ├── data_ingestion.py         # S3/URL dataset fetcher & raw split
│   │   └── data_preprocessing.py     # Lemmatization, stopword & regex cleaner
│   ├── features/
│   │   └── feature_engineering.py    # Vectorizer fitting and transformation
│   ├── models/
│   │   ├── model_building.py         # Estimator fit & artifact serialization
│   │   ├── model_evaluation.py       # Metrics calculation and MLflow logging
│   │   └── register_model.py         # Automated model registration to MLflow
│   └── logger/                       # Custom rotating execution logger
├── tests/
│   ├── test_flask_app.py             # Route availability and payload unit tests
│   └── test_model.py                 # Model shape, signature, and performance tests
├── deployment.yaml                   # Kubernetes Deployment & LoadBalancer Service
├── Dockerfile                        # Multi-stage container definition
├── dvc.yaml                          # DVC pipeline DAG specifications
├── params.yaml                       # Centralized hyperparameter configurations
├── setup.py                          # Local package installer
└── projectflow.txt                   # Execution blueprint & command logs

```

---

## 📸 Production Deployment & Pipeline Showcase

### 1. Automated CI/CD Execution (GitHub Actions)

> *Automated end-to-end CI/CD workflow executing on push to `main`: triggers DVC reproduction, validates model performance benchmarks, promotes passing models to production, builds the Docker image, pushes to AWS ECR, and updates the AWS EKS cluster.*

---

### 2. AWS Elastic Container Registry (ECR)

> *Private repository (`flask-app`) storing versioned, lightweight application images ready for Kubernetes pod distribution.*

---

### 3. Live Flask Web Application on AWS EKS

> *Interactive sentiment inference interface served live via an AWS Elastic Load Balancer endpoint exposed on port 5000.*

---

### 4. Real-Time Observability (Prometheus & Grafana)

> *Prometheus scraping live `/metrics` endpoints and Grafana dashboards visualizing request latency, throughput rates, and positive/negative sentiment prediction distribution.*

---

## 🔬 Experiment Tracking (MLflow & DagsHub)

### Step 3: Remote MLflow Setup

1. Connect your repository to **[DagsHub](https://dagshub.com)**.
2. Export your DagsHub authentication token and tracking URI:
```bash
export MLFLOW_TRACKING_URI="[https://dagshub.com/](https://dagshub.com/)<username>/IMDb-Sentiment-Analysis-MLOps.mlflow"
export MLFLOW_TRACKING_USERNAME="<your-username>"
export MLFLOW_TRACKING_PASSWORD="<your-dagshub-token>"

```



### Step 4: Iterative Model Experiments

* **Baseline Experiment**: Logistic Regression with basic CountVectorizer feature space.
* **Feature Extraction Benchmarks**: CountVectorizer (Bag of Words) vs. TfidfVectorizer across Logistic Regression, MultinomialNB, and Gradient Boosting.
* **Hyperparameter Optimization**: Grid optimization over regularization parameters ($C \in [0.1, 1.0, 10.0]$, Penalty: $L_1, L_2$) logged directly to DagsHub.

---

## 📦 Data & Pipeline Orchestration (DVC + AWS S3)

### Step 5: DVC Pipeline Pipeline Automation

Every modular pipeline stage is chained with explicit inputs, outputs, and tracked parameters in `dvc.yaml`:

```bash
# Execute end-to-end pipeline reproduction
dvc repro

```

Inspect pipeline status:

```bash
dvc status

```

### Step 6: Remote Data Versioning via AWS S3

Configure your AWS S3 bucket as the remote DVC storage:

```bash
dvc remote add -d myremote s3://<your-s3-bucket-name>/dvcstore
dvc push

```

---

## 🧪 Automated Testing & Model Promotion

### Step 7: Continuous Integration Automated Gates

Before creating production container artifacts, the CI pipeline enforces strict automated quality gates:

1. **Model Signature & Shape Validation**: Confirms that feature transformation shapes match the expected vector dimensions.
2. **Performance Benchmark Verification**: Asserts that new candidate models exceed the minimum accuracy, recall, and F1-score thresholds compared to active models.
3. **Automated Promotion to Production**:
```bash
python scripts/promote_model.py

```


Transitions the candidate model in the MLflow Model Registry from `Staging` ➔ `Production` while archiving obsolete versions.
4. **Flask Unit Testing**: Validates standard HTTP status codes (`200 OK`) and prediction routes using `pytest`.

---

## 🚢 Containerization & Kubernetes Deployment (AWS EKS)

### Step 8: Production Docker Image Build

The application image packages the lightweight Flask server, serialized vectorizers, and client configurations:

```bash
docker build -t flask-app:latest .

```

### Step 9: AWS EKS Cluster Provisioning

1. Ensure `aws-cli`, `kubectl`, and `eksctl` are installed and configured:
```bash
eksctl create cluster \
  --name flask-app-cluster \
  --region us-east-1 \
  --nodegroup-name flask-app-nodes \
  --node-type t3.small \
  --nodes 1 \
  --managed

```


2. Update local `kubeconfig`:
```bash
aws eks update-kubeconfig --region us-east-1 --name flask-app-cluster

```



### Step 10: Kubernetes Deployment & Service Manifest

Apply the declarative configuration in `deployment.yaml`:

```bash
kubectl apply -f deployment.yaml

```

Verify active pods and external LoadBalancer status:

```bash
kubectl get pods
kubectl get svc

```

Access the service live via the assigned `EXTERNAL-IP` at port `5000`:

```text
http://<EXTERNAL-LOAD-BALANCER-IP>:5000

```

---

## 📊 Live Observability: Prometheus & Grafana Setup

### Step 11: Dedicated Observability Server Setup

Launch an Ubuntu EC2 instance (`t3.medium`) with inbound rules allowing ports `9090` (Prometheus) and `3000` (Grafana).

### Step 12: Configure Prometheus Scraping

Add the EKS LoadBalancer `/metrics` target into `/etc/prometheus/prometheus.yml`:

```yaml
scrape_configs:
  - job_name: 'flask-sentiment-app'
    scrape_interval: 15s
    static_configs:
      - targets: ['<EKS-LOAD-BALANCER-HOST>:5000']

```

Restart Prometheus service:

```bash
sudo systemctl restart prometheus

```

### Step 13: Grafana Metrics Visualization

1. Access Grafana at `http://<EC2_PUBLIC_IP>:3000` (Default credentials: `admin` / `admin`).
2. Add Prometheus as the primary Data Source (`http://<PROMETHEUS_IP>:9090`).
3. Build dashboards tracking key metrics:
* `app_request_count_total`: Request volume across `/` and `/predict`.
* `request_latency_seconds_bucket`: Latency distribution percentiles.
* `model_prediction_count_total`: Sentiment classification breakdown (Positive `1` vs. Negative `0`).



---

## 🔐 GitHub Actions Secrets Setup

To enable zero-touch automation on push, configure the following secrets under **Repository ➔ Settings ➔ Secrets and variables ➔ Actions**:

| Secret Name | Description |
| --- | --- |
| `CAPSTONE_TEST` | DagsHub User / Project Access Token |
| `AWS_ACCESS_KEY_ID` | AWS IAM user access key |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM user secret key |
| `AWS_DEFAULT_REGION` | Target AWS region (e.g., `us-east-1`) |
| `AWS_ACCOUNT_ID` | 12-digit AWS account identifier |
| `ECR_REPO` | Name of your AWS ECR repository (`flask-app`) |

---

## 🧹 AWS Teardown & Resource Cleanup

To prevent unnecessary AWS cloud billing after testing:

```bash
# 1. Delete Kubernetes deployment and LoadBalancer service
kubectl delete -f deployment.yaml

# 2. Terminate the EKS cluster and provisioned CloudFormation stacks
eksctl delete cluster --name flask-app-cluster --region us-east-1

# 3. Stop and terminate Prometheus & Grafana EC2 instances
# 4. Clean out and delete the ECR repository
aws ecr delete-repository --repository-name flask-app --force

```

---

## 🎯 Workflow Summary

```text
Local Experimentation (MLflow) 
       │
       ▼
DVC Pipeline Orchestration ➔ Remote Storage (AWS S3)
       │
       ▼
GitHub Actions CI/CD Pipeline
       ├── 1. Automated Model Tests & Benchmark Asserts
       ├── 2. Model Registry Promotion (Staging ➔ Production)
       ├── 3. Docker Container Build
       ├── 4. Push Image to AWS ECR
       └── 5. Deploy Pods to AWS EKS Cluster
                 │
                 ▼
Real-Time Monitoring (Prometheus & Grafana)

```

```

```
