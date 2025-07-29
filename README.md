# FuelAI.Infrastructure.project

# Fuel.AI Cloud Infrastructure Project

This repository documents a **7-day multi-cloud infrastructure project** showcasing cloud automation, security, and monitoring.  
The project is designed to simulate an **AI data pipeline** for a company like **Fuel.AI**, leveraging **Azure + GCP**.

---

## **Day 1: Architecture & Design**
### **Goals**
- Design a multi-cloud architecture using Azure (AKS, Blob Storage) and GCP (GKE, Cloud Storage).
- Prepare for automated deployments with Infrastructure-as-Code (Terraform).

### **Architecture Diagram**
![Fuel.AI Architecture](fuelaidiagram.png)

---

## **Project Overview**
- **Clouds:** Azure & GCP
- **Services:** AKS, GKE, Blob Storage, Cloud Storage, Azure Monitor, GCP Cloud Ops
- **Tools:** Terraform, Docker, Kubernetes, Grafana



---

## 📅 Day 2: Azure Infrastructure Deployment (Terraform)

### ✅ Resources Deployed
Using Terraform, the following Azure resources were successfully deployed:

- **Resource Group:** `fuelai-rg` – Logical container for managing assets
- **Virtual Network (VNet):** `fuelai-vnet` – Private, secure network for workloads
- **Subnet:** `fuelai-subnet` – Segmented portion of the VNet for compute resources
- **Storage Account:** `fualaistorage` – Blob storage for AI datasets and pipeline staging

These resources lay the foundation for secure, scalable, and cloud-native AI workloads to be run in Azure (e.g., AKS, containerized data processors).

---

## 🛠️ Azure CLI + Terraform Troubleshooting Guide

This section documents **real-world issues encountered and solved** during Day 2 of the Fuel.AI Cloud Infrastructure Project. These reflect actual roadblocks I faced while building infrastructure using Terraform and Azure CLI on a Linux machine.

---

### 🔐 Authentication Errors

**❌ Error:**
ERROR: Please run 'az login' to setup account.

**✅ What I Did:**
```bash
az login --use-device-code
az account show



🧾 Missing Subscription or Tenant ID
❌ Error:
cpp
Copy
Edit
tenant ID was not specified and the default tenant ID could not be determined

✅ What I Did:
Retrieved subscription and tenant IDs:

bash
Copy
Edit
az account show --output json
Added them manually to the provider block in main.tf:

hcl
Copy
Edit
provider "azurerm" {
  features {}
  subscription_id = "<my-subscription-id>"
  tenant_id       = "<my-tenant-id>"
}


🧩 Invalid Subscription ID
❌ Error:

pgsql
Copy
Edit
The provided subscription ID '...' is not known by Azure CLI

✅ What I Did:

Checked for typos or missing characters.

Copied fresh ID with:

bash
Copy
Edit
az account show
Pasted directly into main.tf.

🧱 Locked APT (Linux Package Manager)
❌ Error:
csharp
Copy
Edit
Could not get lock /var/lib/dpkg/lock-frontend

✅ What I Did:
bash:
Copy
Edit
ps aux | grep apt
sudo kill -9 <PID>
sudo rm /var/lib/dpkg/lock-frontend
sudo dpkg --configure -a


🔒 Resource Provider Registration Fails
❌ Error:

go
Copy
Edit
Encountered an error whilst ensuring Resource Providers are registered. HTTP response was nil
✅ What I Did:
Went to Azure Portal → Subscriptions → Resource Providers
Manually registered:
Microsoft.Storage
Microsoft.Network
Microsoft.ManagedIdentity

Re-ran:
bash:
Copy
Edit
terraform apply


🌐 CLI Token Issues
❌ Error:
nginx
Copy
Edit
could not configure AzureCli Authorizer

✅ What I Did:
bash
Copy
Edit
az logout
az login --use-device-code


** After resolving issues, I always verified:

bash:
Copy
Edit
az account show
az account list --output table
Then continued with:

bash:
Copy
Edit
terraform init
terraform plan
terraform apply




---

## **Roadmap**
- **Day 1:** Architecture & Planning (This README)
- **Day 2:** Terraform setup for Azure & GCP (VNet/VPC + storage + bastion host)
- **Day 3:** Kubernetes clusters (AKS + GKE) & container deployment
- **Day 4:** Data pipeline simulation
- **Day 5:** Security & IAM
- **Day 6:** Monitoring & logging
- **Day 7:** Documentation & final polish

