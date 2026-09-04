# Multi-Cloud Deployment Guide

## 1. Build and test locally

```bash
cd backend
./mvnw test

cd ../frontend
npm ci
npm run build
```

## 2. Build the containers

From the repository root:

```bash
docker build -t <docker-user>/student-registration-backend:<tag> ./backend
docker build --build-arg VITE_API_URL=/api   -t <docker-user>/student-registration-frontend:<tag> ./frontend
```

## 3. Provision AWS infrastructure

```bash
cd infrastructure/aws/terraform
cp terraform.tfvars.example terraform.tfvars
# Put the real DB password in terraform.tfvars or use a secure variable source.
terraform init
terraform plan
terraform apply
```

Then configure kubectl for the EKS cluster and install the AWS Load Balancer Controller as required by the cluster.

## 4. Provision Azure bootstrap infrastructure

```bash
cd infrastructure/azure/bootstrap-vm
cp terraform.tfvars.example terraform.tfvars
terraform init
terraform plan
terraform apply
```

The supplied Azure project used these VMs as part of its DevSecOps/Kubernetes lab setup; AKS creation/configuration is kept as a deployment step rather than falsely represented as Terraform-managed here.

## 5. Deploy the Helm release

AWS:

```bash
helm upgrade --install student-registration   deploy/helm/aws   --namespace student-registration   --create-namespace
```

Azure:

```bash
helm upgrade --install student-registration   deploy/helm/azure   --namespace student-registration   --create-namespace
```

Review the cloud-specific `values.yaml` files before deployment and update image tags and secrets securely.

## 6. GitOps

Apply the corresponding Argo CD application manifest from:

- `deploy/argocd/aws/`
- `deploy/argocd/azure/`

Do not commit Kubernetes secrets, cloud credentials, Terraform state, SSH private keys, or Docker Hub tokens.
