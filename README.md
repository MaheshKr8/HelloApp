# CI/CD with Azure DevOps, Terraform, Docker, and AKS

## Project Structure

```text
HelloApp/
├── app/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
├── infra/
│   ├── main.tf           # Defines Azure resources (AKS, or App Service, etc.)
│   ├── provider.tf       # Configures Azure provider and subscription
│   ├── variables.tf      # Defines input variables for the infrastructure
│   ├── outputs.tf        # Defines outputs like AKS kubeconfig or App Service URL
│   └── terraform.tfvars  # Optional values for variables
├── k8s/
│   └── deployment.yaml   # Kubernetes deployment manifest
├── pipelines/
│   └── app-pipeline.yml  # Application deployment to AKS
└── README.md
Prerequisites
Azure CLI

Azure DevOps account

Docker Hub account

1. Azure Setup
Login to Azure
bash
Copy code
az login user1
az account set --subscription "<your-subscription-id>"
Create Service Principal for Terraform RBAC
bash
Copy code
az ad sp create-for-rbac --name "terraform-sp" --role Contributor --scopes /subscriptions/<subscription-id>
Output:

json
Copy code
{
  "appId": "<client-id>",
  "displayName": "terraform-sp",
  "password": "<client-secret>",
  "tenant": "<tenant-id>"
}
Set environment variables for Terraform:

bash
Copy code
export ARM_CLIENT_ID="<client-id>"
export ARM_CLIENT_SECRET="<client-secret>"
export ARM_TENANT_ID="<tenant-id>"
export ARM_SUBSCRIPTION_ID="<subscription-id>"
2. Verify Terraform
bash
Copy code
terraform -v
3. Azure DevOps Setup
Service Connections:

Azure RM Connection → Connect to your subscription

Docker Hub Connection → For container images

4. Terraform Workflow
bash
Copy code
cd infra
terraform init          # Initialize Terraform
terraform plan          # Preview infrastructure changes
terraform apply -auto-approve  # Apply configuration
terraform destroy -auto-approve  # Optional: destroy resources
5. Application Deployment
Commit code to main branch → triggers app-pipeline.yml.

Pipeline steps:

Build Docker image

Push to Azure Container Registry (ACR)

Deploy app to AKS using Kubernetes manifests

6. Access Application
Get the external IP of the LoadBalancer service:

bash
Copy code
kubectl get svc hello-world-service
Visit http://<EXTERNAL-IP> to see the app 🚀

Assumptions
Azure DevOps service connections already set:

my-acr-service-connection → Docker registry service connection to ACR

my-aks-acr-service-connection → AKS service connection

Improvements for Production
Use Helm charts instead of raw YAML

Enable monitoring (Prometheus/Grafana)

Add Azure Key Vault for secrets

Enable autoscaling for pods and nodes

Implement GitOps for infra pipeline

CI/CD Pipeline Flow
text
Copy code
+-------------------------+
|      Developer          |
|  (Push Code to GitHub)  |
+-----------+-------------+
            |
            v
+-------------------------+
|   Azure DevOps Repo      |
| ( + App Code)           |
+-----------+-------------+
            |
            v
+-------------------------+
|   Pipeline Trigger       |
|  (manual or main branch)|
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 1: Terraform Infra|
+-------------------------+
| - terraform init        |
| - terraform plan        |
| - terraform apply       |
| Creates:                |
| - Resource Group        |
| - AKS Cluster           |
| - App Service / Plan    |
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 2: Docker Build    |
+-------------------------+
| - Docker build           |
| - Docker push to ACR     |
+-----------+-------------+
            |
            v
+-------------------------+
| Stage 3: Kubernetes / AKS Deploy |
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
| Monitoring / Logs        |
| - Azure Monitor          |
| - Container Insights     |
+-------------------------+
