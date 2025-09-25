# CI/CD with Azure DevOps, Terraform, Docker, and AKS

## Steps to Run

1. **Infra Setup**
   add  main.tf,output,provider,variables
HelloApp/
├── app/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
├── infra/
│   ├── main.tf           # Defines Azure resources (AKS, App Service, etc.)
│   ├── provider.tf       # Configures Azure provider and subscription
│   ├── variables.tf      # Defines input variables for the infrastructure
│   ├── outputs.tf        # Defines outputs like AKS kubeconfig or App Service URL
│   └── terraform.tfvars  # (Optional) Provide values for variables
├── k8s/
│   └── deployment.yaml   # Kubernetes deployment manifest
├── pipelines/
│   ├── app-pipeline.yml   # for applicaiton deployment to AKS Cluster.
│   
└── README.md

Prerequisites
Azure CLI

b# Azure DevOps + Terraform + AKS Setup Guide

## 1. Login to Azure
az account set --subscription "<your-subscription-id>"
For Secrets and RBAC
az ad sp create-for-rbac --name "terraform-sp" --role Contributor --scopes /subscriptions/dcxxxxxxxxxxxxxxxxxxxxx

output:
{
  "appId": "vvxyxxxxxxad50",
  "displayName": "terraform-sp",
  "password": "password",
  "tenant": "your tenant"

export ARM_CLIENT_ID=“" export ARM_CLIENT_SECRET="" export ARM_TENANT_ID="" export ARM_SUBSCRIPTION_ID="dxxxxxx"


2. Verify Terraform


terraform -v  

3. Azure DevOps Setup

Service Connections
Azure RM Connection → Connect to your subscription

Docker Hub Connection → For container images

Accounts
Azure DevOps account

Docker Hub account

4. Terraform Workflow
Navigate to infra folder:


cd infra
Initialize Terraform:


terraform init
Preview infrastructure changes:


terraform plan
Apply the configuration:

terraform apply -auto-approve
Destroy resources (if needed):

terraform destroy -auto-approve


#####.  



2. **Application Deployment**
   - Commit code to `main` branch → triggers `app-pipeline.yml`
   - Steps:
     - Build Docker image
     - Push to ACR
     - Deploy app to AKS using Kubernetes manifests

3. **Access App**
   - Get the external IP of the LoadBalancer service:
     ```sh
     kubectl get svc hello-world-service
     ```
   - Visit `http://<EXTERNAL-IP>` to see Hello World 🚀

## Assumptions
- Azure DevOps service connections already set:
  - `my-acr-service-connection` → Docker registry service connection to ACR
  - `my-aks-acr-service-connection` → AKS service Connection
  

## Improvements for Production
- Use Helm charts instead of raw YAML.
- Enable monitoring (Prometheus/Grafana).
- Add Key Vault for secret management.
- Enable autoscaling for pods and nodes.
- infra pipline and practice Gitops
+-------------------------+
|      Developer          |
|  (Push Code to GitHub)  |
+-----------+-------------+
            |
            v
+-------------------------+
|   Azure DevOps Repo      |
| ( + App Code)   |
+-----------+-------------+
            |
            v
+-------------------------+
|   Pipeline Trigger       |
|  (manual or main branch) |
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 1: Terraform Infra|
+-------------------------+
| - terraform init         |
| - terraform plan         |
| - terraform apply        |
|                         |
| Creates:                 |
| - Resource Group         |
| - AKS Cluster   or         |
| - App Service / Plan     |
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 2: Docker Build    |
+-------------------------+
| - Docker build           |
| - Docker push to Docker  |
|   Hub                    |
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 3: Kubernetes /    |
|         AKS Deploy       |
+-------------------------+
| - Install kubectl        |
| - Get AKS credentials    |
| - Apply k8s manifests    |
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 4: App Deployment  |
+-------------------------+
| - Docker image deployed  |
|   to AKS or App Service  |
| - Verify service running |
+-------------------------+
            |
            v
+-------------------------+
|     Monitoring / Logs    |
| - Azure Monitor          |
| - Container Insights     |
+-------------------------+
