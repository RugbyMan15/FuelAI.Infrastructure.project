# Fuel.AI Cloud Infrastructure Project

This repository documents a **7-day multi-cloud infrastructure project** showcasing cloud automation, security, and monitoring.  
The project simulates an **AI data pipeline** for a company like **Fuel.AI**, leveraging both **Azure and GCP** using infrastructure-as-code, Kubernetes, and cross-cloud communication.

---

## **Day 1: Architecture & Planning**

### 🎯 Goals
- Design a multi-cloud infrastructure leveraging:
  - **Azure**: AKS, Blob Storage, Azure Monitor
  - **GCP**: GKE, Cloud Storage, Cloud Ops
- Prepare for automated deployments using **Terraform**
- Support containerized pipelines and observability tools

### 📊 Architecture Diagram
![Fuel.AI Architecture](fuelaidiagram.png)

---

## **Project Overview**
- **Clouds Used**: Azure & GCP
- **Key Services**: AKS, GKE, Storage Accounts, VPC/VNet, LoadBalancers
- **Tools**: Terraform, Azure CLI, gcloud CLI, Docker, Kubernetes, Grafana

---

## **Day 2: Azure Infrastructure Deployment**

### ✅ Resources Deployed
Using Terraform in `fuelai-cloud-infra-project/terraform/azure/`:
- **Resource Group**: `fuelai-rg`
- **Virtual Network (VNet)**: `fuelai-vnet`
- **Subnet**: `fuelai-subnet`
- **Storage Account**: `fualaistorage`

### 🛠️ Terraform Setup
```bash
az login --use-device-code
az account show
terraform init
terraform plan
terraform apply
```

### 🐞 Errors & Fixes

- ❌ **"az login" not configured**  
  ✅ Fix:  
  ```bash
  az login --use-device-code
  ```

- ❌ **Tenant ID missing**  
  ✅ Fix:
  ```hcl
  provider "azurerm" {
    features {}
    subscription_id = "<your-subscription-id>"
    tenant_id       = "<your-tenant-id>"
  }
  ```

- ❌ **Invalid Subscription ID**  
  ✅ Fix:
  ```bash
  az account show
  ```

- ❌ **APT lock issue on Linux**  
  ✅ Fix:
  ```bash
  sudo rm /var/lib/dpkg/lock-frontend
  sudo dpkg --configure -a
  ```

- ❌ **Azure Resource Providers not registered**  
  ✅ Fix: Register them via Azure Portal or CLI

- ❌ **Azure CLI token issue**  
  ✅ Fix:
  ```bash
  az logout
  az login --use-device-code
  ```

---

## **Day 2 (Part 2): GCP Infrastructure Deployment**

### ✅ Resources Deployed
Using Terraform in `fuelai-cloud-infra-project/terraform/gcp/`:
- **VPC**: `fuelai-vpc`
- **Subnet**: `fuelai-subnet`
- **Cloud Storage Bucket**: `fuelai-ml-datasets`

### 🛠️ Terraform Setup
```bash
gcloud auth application-default login
gcloud config set project neural-period-467301-t9
gcloud services enable compute.googleapis.com storage.googleapis.com iam.googleapis.com cloudresourcemanager.googleapis.com serviceusage.googleapis.com container.googleapis.com
terraform init
terraform plan
terraform apply
```

### 🐞 Errors & Fixes

- ❌ **Missing cloud-platform scope**  
  ✅ Fix:
  ```bash
  gcloud auth application-default login
  ```

- ❌ **No credentials loaded**  
  ✅ Fix:
  ```bash
  export GOOGLE_APPLICATION_CREDENTIALS="$HOME/.config/gcloud/application_default_credentials.json"
  ```

- ❌ **Project not set**  
  ✅ Fix:
  ```bash
  gcloud config set project neural-period-467301-t9
  ```

- ❌ **Quota project mismatch warning**  
  ✅ Fix:
  ```bash
  gcloud auth application-default set-quota-project neural-period-467301-t9
  ```

- ❌ **Invalid Terraform block**  
  ✅ Fix:
  ```hcl
  force_destroy = true  # Not 'force destroy'
  ```

---

## **Day 3: Kubernetes Cluster Deployment (AKS + GKE)**

### ✅ Azure AKS Deployment

**Get credentials:**
```bash
az aks get-credentials --resource-group fuelai-rg --name fuelai-aks
```

**Create namespace and deploy nginx:**
```bash
kubectl create namespace pipeline
kubectl create deployment data-processor --image=nginx -n pipeline
kubectl expose deployment data-processor --port=80 --type=LoadBalancer -n pipeline
kubectl get svc -n pipeline
```

### ✅ GCP GKE Deployment

**Get credentials and install plugin:**
```bash
gcloud container clusters get-credentials fuelai-gke --region us-west2 --project neural-period-467301-t9
sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin
```

**Create namespace and deploy data source:**
```bash
kubectl create namespace pipeline
kubectl run data-source --image=busybox --restart=Never --namespace=pipeline --command -- sh -c 'while true; do echo "{"timestamp": "$(date)", "value": $RANDOM}" >> /data/stream.log; sleep 3; done'
```

---

## **Day 4: Data Pipeline Simulation (GKE ➝ AKS)**

### 🛰️ AKS Setup
```bash
kubectl config use-context aks-fuelai
kubectl create namespace pipeline
kubectl create deployment data-processor --image=nginx -n pipeline
kubectl expose deployment data-processor --port=80 --type=LoadBalancer -n pipeline
kubectl get svc -n pipeline
```

**Sample Output:**
```
data-processor   LoadBalancer   10.0.128.145    13.91.67.194    80:32160/TCP
```

**Test endpoint:**
```bash
curl http://13.91.67.194
```

---

### 🌐 GKE Setup
```bash
kubectl config use-context gke-fuelai
kubectl create namespace pipeline
kubectl run data-source --image=busybox --restart=Never --namespace=pipeline --command -- sh -c 'while true; do echo "{"timestamp": "$(date)", "value": $RANDOM}" >> /data/stream.log; sleep 3; done'
kubectl exec -n pipeline data-source -- wget -qO- http://13.91.67.194
kubectl exec -n pipeline data-source -- sh -c 'while true; do wget -qO- http://13.91.67.194; sleep 5; done'
```

---

### 🐞 Troubleshooting Summary

- ❌ `pods "data-source" not found` → ✅ Fix: Switched to GKE context, redeployed pod.
- ❌ `sh: syntax error: unexpected ";"` → ✅ Fix: Quoted commands properly.
- ❌ `wget: can't connect to remote host` → ✅ Fix: Waited for LoadBalancer to provision.
- ❌ `no context exists with the name "aks-fuelai"` → ✅ Fix:
```bash
kubectl config rename-context fuelai-aks aks-fuelai
kubectl config rename-context gke_neural-period-467301-t9_us-west2_fuelai-gke gke-fuelai
```

---

## ✅ Final Verification
- Cross-cloud HTTP requests succeeded (GCP ➝ Azure).
- Multi-cloud Kubernetes communication validated.
- Infrastructure is repeatable and production-relevant.

---

## **Roadmap**
- ✅ Day 1: Architecture & Planning
- ✅ Day 2: Terraform setup for Azure & GCP (VNet/VPC + Storage)
- ✅ Day 3: Kubernetes Clusters (AKS & GKE)
- ✅ Day 4: Data Pipeline Simulation
- 🔜 Day 5: Security & IAM
- 🔜 Day 6: Monitoring & Logging
- 🔜 Day 7: Final Documentation & Polish
