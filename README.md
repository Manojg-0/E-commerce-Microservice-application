## 🚀 E-Commerce Microservices Deployment on AWS EKS (Manual Setup)

This project demonstrates the deployment of a containerized **E-commerce microservices application** on **AWS EKS**, using **Terraform**, **Docker**, and **Kubernetes**, with a monitoring setup powered by **Prometheus and Grafana**.

Unlike automated pipelines, this setup focuses on **manual deployment and operational understanding** of each component.

---

## 🧩 Architecture Overview

* **Infrastructure as Code:** Terraform provisions EC2 & EKS
* **Containerization:** Docker builds microservices
* **Registry:** AWS ECR stores images
* **Orchestration:** Kubernetes (EKS) manages services
* **Monitoring:** Prometheus + Grafana via `monitor.sh`

---

## ⚙️ Prerequisites

Make sure you have:

* AWS Account with proper IAM permissions
* Terraform installed
* Docker installed
* kubectl installed
* AWS CLI configured
* Helm installed

---

## 🏗️ Step 1: Provision Infrastructure

Navigate to the Terraform folder:

```bash
terraform init
terraform apply -auto-approve
```

This will:

* Launch EC2 instance
* Install required tools (`userdata.sh`)
* Create EKS cluster

---

## ☸️ Step 2: Configure kubectl

```bash
aws eks --region ap-northeast-1 update-kubeconfig --name demo-cluster
kubectl get nodes
```

---

## 🐳 Step 3: Build and Push Docker Images

Login to AWS ECR:

```bash
aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.ap-northeast-1.amazonaws.com
```

Build and push each microservice:

```bash
docker build -t <service-name> .
docker tag <service-name>:latest <ECR-REPO-URL>:latest
docker push <ECR-REPO-URL>:latest
```

---

## 📦 Step 4: Deploy Microservices to Kubernetes

Apply Kubernetes manifests:

```bash
kubectl apply -f k8s/
```

Verify:

```bash
kubectl get pods
kubectl get svc
```

---

## 🌐 Step 5: Access Application

```bash
kubectl get svc frontend-external
```

Open:

```text
http://<EXTERNAL-IP>
```

---

## 📊 Step 6: Setup Monitoring (Prometheus + Grafana)

Run the provided script:

```bash
chmod +x monitor.sh
./monitor.sh
```

---

## 🛠️ monitor.sh Script

This script installs the monitoring stack using Helm.

```bash
#!/bin/bash

# Add Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Create namespace
kubectl create namespace monitor

# Install Prometheus & Grafana stack
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitor

# Expose Grafana
kubectl patch svc prometheus-grafana -n monitor \
  -p '{"spec": {"type": "LoadBalancer"}}'

echo "Monitoring stack deployed!"

# Get Grafana URL
kubectl get svc -n monitor prometheus-grafana
```

---

## 🔐 Grafana Access

Get external IP:

```bash
kubectl get svc -n monitor prometheus-grafana
```

Retrieve password:

```bash
kubectl get secret prometheus-grafana -n monitor \
  -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Login:

* **URL:** `http://<EXTERNAL-IP>`
* **Username:** `admin`
* **Password:** Retrieved from secret

---

## 🔍 Key Features

* ✅ Manual deployment flow for better learning
* ✅ Infrastructure provisioning using Terraform
* ✅ Scalable Kubernetes deployment on EKS
* ✅ Containerized microservices using Docker
* ✅ Monitoring using Prometheus & Grafana via script

---

## 📁 Project Structure

```
.
├── EKS-cluster-terraform/         # Infrastructure provisioning
├── Microservices/                 # Microservices source code
      ├── kubernetes-manifests/    # Kubernetes manifests    
├── Monitor.sh                      # Monitoring setup script
└── README.md
```

---

## ⚠️ Notes

* Ensure EC2 IAM role has required permissions (EKS, ECR, EC2)
* Monitoring stack is deployed in `monitor` namespace
* Microservices are deployed in `default` namespace

---

## 🧠 Learning Outcome

This project helps in understanding:

* Kubernetes deployments and services
* AWS EKS cluster management
* Docker image lifecycle (build → push → deploy)
* Monitoring setup using Prometheus and Grafana
* End-to-end manual DevOps workflow
