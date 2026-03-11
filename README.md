# E-commerce Microservice Application

This project is a cloud-native e-commerce system deployed on **Kubernetes**. The application allows users to browse products, add items to a cart, and complete purchases.

The system is built using multiple backend services that communicate with each other using **gRPC**. Each service runs in its own container and is managed by Kubernetes.

Application infrastructure is provisioned using **Terraform**, and the services are containerized using **Docker**.

---

# Architecture

The application consists of several services working together to process user requests. The frontend service handles incoming requests and communicates with other backend services to complete actions such as browsing products, managing carts, and completing checkout. 

The application is deployed on Kubernetes using Amazon EKS.Each service runs as a container and communicates with other services using gRPC.

### High-Level Architecture

![Architecture Diagram](docs/architecture.png)

All services run as containers and are orchestrated using Kubernetes.

---

# DevOps / Infrastructure Flow
 Developer
   │
   ▼
GitHub Repository
   │
   ▼
Run docker_image_buid_push.sh
   │
   ▼
Docker Images Built
   │
   ▼
Push Images to Amazon ECR
   │
   ▼
Terraform Creates EKS Cluster
   │
   ▼
Kubernetes Deployments
   │
   ▼
Application Running on EKS

# Technologies Used

* **Docker** – Containerization of services
* **Kubernetes** – Container orchestration
* **Terraform** – Infrastructure provisioning
* **gRPC** – Communication between services
* **Redis** – Storage for shopping cart data

The project can be deployed on Kubernetes clusters such as:

* **Amazon EKS**
* **Google Kubernetes Engine**
* **Azure Kubernetes Service**

---

# How to Run the Project

### 1. Clone the repository

```bash
git clone https://github.com/Manojg-0/E-commerce-Microservice-application.git
cd E-commerce-Microservice-application
```

---

### 2. Create the Kubernetes cluster

Navigate to the Terraform directory and provision the infrastructure:

```bash
cd EKS-cluster-terraform
terraform init
terraform plan
terraform apply
```

This will create the Kubernetes cluster.

---

### 3. Build and push Docker images

Navigate to the microservices directory and run the build script:

```bash
cd micro-service-demo
chmod +x docker_image_buid_push.sh
./docker_image_buid_push.sh
```

This script builds the Docker images for the services and pushes them to **Amazon Elastic Container Registry**.

---

### 4. Deploy the application

Deploy the services to Kubernetes:

```bash
kubectl apply -f release/kubernetes-manifests.yaml
```

---

### 5. Verify deployment

Check if the pods are running:

```bash
kubectl get pods
```

Check the services:

```bash
kubectl get svc
```

---

### 6. Access the application

Get the external IP of the frontend service:

```bash
kubectl get svc frontend-external
```

Open the external IP in your browser to access the application.
