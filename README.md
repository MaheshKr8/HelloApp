# CI/CD with Azure DevOps, Terraform, Docker, and AKS

## Steps to Run

1. **Infra Setup**
   - Go to Azure DevOps → Pipelines → Run `infra-pipeline.yml`
   - Provisions: 
     - Resource Group
     - Azure Container Registry (ACR)
     - AKS Cluster

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
  - `my-service-connection` → ARM connection (for Terraform + AKS)
  - `my-acr-service-connection` → Docker registry service connection to ACR

## Improvements for Production
- Add automated tests before deploy.
- Use Helm charts instead of raw YAML.
- Enable monitoring (Prometheus/Grafana).
- Add Key Vault for secret management.
- Enable autoscaling for pods and nodes.
