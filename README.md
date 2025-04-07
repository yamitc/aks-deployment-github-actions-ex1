### Task 1 – IaC Pipeline: AKS Cluster with ACR & Internal Ingress Controller

#### Task Description

The task was to create an automated pipeline that provisions an **Azure Kubernetes Service (AKS)** cluster using **Infrastructure as Code**. The pipeline needed to deploy:
- An **AKS cluster**
- An **ingress controller** (any type)
- An **Azure Container Registry (ACR)**

I had the option to use either **GitHub Actions** or **Azure DevOps Pipelines** for the implementation.

> **Bonus requirement**: Deploy the AKS cluster in **private network mode**, meaning the Kubernetes API server should **not be accessible from the public internet**.

---

### Solution Overview

To implement the task, I chose to use **GitHub Actions** for automating the deployment process.

I decided to go beyond the basic requirements and include the **bonus task** of deploying the AKS cluster in **private network mode**. As a result, I created **two separate GitHub Actions pipelines**:

1. **Public AKS Pipeline** (`deploy-aks.yml`)  
   Provisions an AKS cluster with public access to the Kubernetes API server. This pipeline includes:
   - AKS cluster creation
   - Azure Container Registry (ACR) setup
   - Installation of NGINX ingress controller

2. **Private AKS Pipeline** (`deploy-private-aks.yml`)  
   Deploys an AKS cluster with a **private API endpoint**, meaning the Kubernetes API server is only accessible from within a specific virtual network. To interact with the private cluster from outside the VNet (e.g., during pipeline execution), I created a **jumpbox VM** that:
   - Is deployed **inside the same VNet and subnet** as the AKS cluster
   - Has a **public IP address** so that it can be accessed over SSH from GitHub-hosted runners

Both pipelines are triggered manually using `workflow_dispatch` and accept input parameters such as resource group name, cluster name, region, and monitoring preferences. This makes the workflows reusable and adaptable across different environments.

---

### Prerequisites

Before implementing the pipeline, I completed the following setup steps to ensure I had the necessary resources and permissions in Azure:

#### 1. Created a Free Azure Account

To begin, I signed up for a **free Azure account** at [https://azure.microsoft.com/free](https://azure.microsoft.com/free), which provided the base environment for deploying cloud resources. I then proceeded with the initial setup using the Azure Cloud Shell, which offers a convenient CLI interface pre-configured with all the necessary tools for Azure management.

#### 2. Created a Service Principal

A **Service Principal** is a security identity used by applications, services, and automation tools to access Azure resources. It allows the pipeline to authenticate and perform actions on my behalf.

I created the service principal using the following command:

```bash
az ad sp create-for-rbac \
  --name "github-actions-deployer" \
  --role contributor \
  --scopes /subscriptions/$(az account show --query id -o tsv) \
  --sdk-auth
```

This command generates a JSON object that contains the credentials needed to authenticate:

```json
{
  "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "clientSecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  ...
}
```

I then used this output to create a repository secret named **`AZURE_CREDENTIALS`**. This secret is used by the GitHub Actions workflow to authenticate with Azure during pipeline execution.

---


