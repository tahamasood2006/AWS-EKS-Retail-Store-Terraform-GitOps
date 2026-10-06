# AWS EKS Retail Store — Terraform + GitOps

A hands-on DevOps project that deploys a containerized **Retail Store microservices application on Amazon EKS** using **Terraform, Helm, Argo CD, GitHub Actions, Docker, and Amazon ECR**.


**Infrastructure as Code → Container Build → Amazon ECR → GitOps → Amazon EKS**

---

## 🚀 Project Overview

Project uses the AWS Retail Store Sample Application as a practical microservices workload and focuses on the DevOps infrastructure and deployment workflow around it.

The infrastructure is provisioned with Terraform, while application deployments are managed through Argo CD and Helm. GitHub Actions is used for CI to detect service changes, build Docker images, and push them to Amazon ECR.

### Architecture

```text
                         ┌─────────────────────┐
                         │      Developer      │
                         │     git push / PR   │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │    GitHub Actions   │
                         │                     │
                         │  • Detect changes  │
                         │  • Docker build     │
                         │  • Push to ECR      │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │     Amazon ECR      │
                         │   Container Images  │
                         └──────────┬──────────┘
                                    │
                                    ▼
┌──────────────────────────────────────────────────────────────────────┐
│                              AWS                                     │
│                                                                      │
│  ┌────────────────────── VPC ─────────────────────────────────────┐  │
│  │                                                                 │  │
│  │   Public Subnets                    Private Subnets             │  │
│  │   ┌──────────────┐                 ┌─────────────────────────┐  │  │
│  │   │ NLB / Ingress│ ──────────────► │       Amazon EKS        │  │  │
│  │   └──────────────┘                 │                         │  │  │
│  │                                    │  Retail Store Services  │  │  │
│  │                                    │  • UI                   │  │  │
│  │                                    │  • Catalog               │  │  │
│  │                                    │  • Cart                  │  │  │
│  │                                    │  • Orders                │  │  │
│  │                                    │  • Checkout              │  │  │
│  │                                    └────────────┬────────────┘  │  │
│  │                                                 │               │  │
│  │                                    ┌────────────▼────────────┐  │  │
│  │                                    │        Argo CD           │  │  │
│  │                                    │   GitOps / Continuous    │  │  │
│  │                                    │       Delivery           │  │  │
│  │                                    └─────────────────────────┘  │  │
│  │                                                                 │  │
│  │                 NAT Gateway → Internet                          │  │
│  └─────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 🧰 Tech Stack

| Area                   | Technology                                |
| ---------------------- | ----------------------------------------- |
| Cloud                  | AWS                                       |
| Kubernetes             | Amazon EKS                                |
| Infrastructure as Code | Terraform                                 |
| Containerization       | Docker                                    |
| Container Registry     | Amazon ECR                                |
| CI                     | GitHub Actions                            |
| CD / GitOps            | Argo CD                                   |
| Kubernetes Packaging   | Helm                                      |
| Ingress                | NGINX Ingress Controller                  |
| TLS / Certificates     | cert-manager                              |
| Networking             | Amazon VPC, Internet Gateway, NAT Gateway |
| Application            | Retail Store Microservices                |
| Languages              | Java, Go, Node.js                         |

---

## 🏗️ Infrastructure

Terraform provisions the core AWS infrastructure required to run the application.

### AWS Resources

* Amazon VPC
* Public and private subnets
* Internet Gateway
* NAT Gateway
* Amazon EKS cluster
* EKS Auto Mode compute
* EKS cluster encryption using AWS KMS
* Kubernetes subnet tags
* Security group rules
* NGINX Ingress Controller
* Argo CD

The EKS cluster is configured with both public and private API endpoint access, while the cluster compute runs in private subnets.

---

## 📦 Application Services

The retail application is composed of multiple independent services:

* **UI** — Java-based frontend
* **Catalog** — Product catalog service
* **Cart** — Shopping cart service
* **Orders** — Order management service
* **Checkout** — Checkout orchestration service

Each service has its own Dockerfile and Helm chart, allowing services to be built and deployed independently.

---

## 🔄 CI/CD Workflow

### Continuous Integration — GitHub Actions

The CI workflow uses path-based change detection so that only affected services need to be rebuilt.

For example:

```text
Change in src/cart/
       │
       ▼
GitHub Actions
       │
       ├── Detect cart changes
       ├── Configure AWS credentials
       ├── Login to Amazon ECR
       ├── Build Docker image
       ├── Tag image with Git 
       └── Push image to ECR
```


### Continuous Delivery — Argo CD

Argo CD follows the Git repository and deploys the Helm charts to EKS.

The applications are configured with:

* Automated synchronization
* Automatic pruning
* Self-healing
* Helm value files
* Separate Argo CD applications for each service
* Dedicated `retail-store` namespace

This follows a GitOps model where Git acts as the desired state for Kubernetes deployments.

---

## 📁 Repository Structure

```text
.
├── argocd/
│   ├── applications/
│   │   ├── retail-store-cart.yaml
│   │   ├── retail-store-catalog.yaml
│   │   ├── retail-store-checkout.yaml
│   │   ├── retail-store-orders.yaml
│   │   └── retail-store-ui.yaml
│   └── projects/
│       └── retail-store-project.yaml
│
├── src/
│   ├── cart/
│   ├── catalog/
│   ├── checkout/
│   ├── orders/
│   └── ui/
│
├── terraform/
│   ├── main.tf
│   ├── addons.tf
│   ├── argocd.tf
│   ├── security.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── locals.tf
│   └── versions.tf
│
├── ci-cd.yml
├── .gitignore
└── README.md
```

---

## ⚙️ Prerequisites

Before deploying the project, make sure you have:

* AWS account
* AWS CLI
* Terraform >= 1.0
* kubectl
* Helm
* Git
* Docker
* GitHub repository
* Amazon ECR repositories
* AWS credentials with permissions to create the required resources



## 💰 Cost Considerations

This project creates AWS resources that can incur charges, especially:

* Amazon EKS
* NAT Gateway
* Network Load Balancer
* ECR storage
* EKS compute
* AWS networking resources


## 🔐 Security Notes

This repository is intended as a learning and portfolio project.

Before using a similar setup in production:

* Use IAM roles instead of long-lived AWS access keys where possible.
* Store GitHub Actions secrets securely.
* Use GitHub OIDC for AWS authentication instead of static credentials.
* Restrict security group rules to the minimum required access.


## 🎯 What This Project Demonstrates

This project provides practical experience with:

* Infrastructure as Code with Terraform
* Amazon VPC networking
* Amazon EKS
* EKS Auto Mode
* Kubernetes workloads
* Docker containerization
* Amazon ECR
* GitHub Actions CI
* Argo CD GitOps
* Helm charts
* NGINX Ingress
* Kubernetes namespaces
* Kubernetes autoscaling and workload configuration
* AWS security groups
* AWS KMS encryption
* CI/CD troubleshooting
* Cloud infrastructure lifecycle management

---

## 📌 Project Status

This is a **learning and portfolio project** focused on practicing an end-to-end AWS DevOps workflow.

The GitHub Actions workflow currently contains placeholders for the ECR registry/repository and requires AWS credentials/secrets to be configured before it can be used as a complete CI pipeline.


---


## ⭐ Why I Built This

The goal of this project was to move beyond learning individual DevOps tools and understand how they work together in a real deployment workflow:

```text
Terraform
   ↓
AWS VPC + EKS
   ↓
Docker
   ↓
Amazon ECR
   ↓
GitHub Actions
   ↓
Argo CD
   ↓
Helm
   ↓
Kubernetes
   ↓
Retail Application
```

It provides a practical environment for learning how infrastructure, containers, CI/CD, Kubernetes, and GitOps fit together in a cloud-native deployment.
