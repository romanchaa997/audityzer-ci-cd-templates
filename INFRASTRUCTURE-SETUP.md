# Audityzer Infrastructure Setup Guide

## Quick Start

This guide provides step-by-step instructions to set up the complete Audityzer infrastructure on AWS with Kubernetes.

## Prerequisites

### Required Tools
- AWS CLI v2.x
- Terraform v1.5+
- kubectl v1.28+
- Docker 24.x
- Helm 3.x

### AWS Account Requirements
- VPC with appropriate CIDR blocks
- IAM user with programmatic access
- S3 bucket for Terraform state
- DynamoDB table for state locking

## Setup Instructions

### Step 1: Configure AWS Credentials

```bash
# Configure AWS CLI
aws configure
AWS Access Key ID: <your_access_key>
AWS Secret Access Key: <your_secret_key>
Default region name: eu-west-1
Default output format: json
```

### Step 2: Initialize Terraform

```bash
# Copy terraform variables template
cp terraform.tfvars.example terraform.tfvars

# Edit terraform variables
vim terraform.tfvars

# Initialize Terraform
terraform init

# Plan infrastructure
terraform plan -out=tfplan

# Apply infrastructure
terraform apply tfplan
```

### Step 3: Setup Kubernetes Cluster

```bash
# Get kubeconfig from EKS
aws eks update-kubeconfig \
  --region eu-west-1 \
  --name audityzer-prod-cluster

# Verify cluster connection
kubectl cluster-info

# Deploy Kubernetes manifests
kubectl apply -f k8s-deployment.yml
```

### Step 4: Configure Environment Variables

```bash
# Copy environment template
cp .env.example .env

# Edit environment variables
vim .env

# Create Kubernetes secrets
kubectl create secret generic audityzer-secrets \
  --from-file=.env \
  -n audityzer-prod
```

### Step 5: Setup Monitoring Stack

```bash
# Install Prometheus Helm Chart
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install prometheus prometheus-community/kube-prometheus-stack \
  -n audityzer-prod

# Install Grafana
helm install grafana grafana/grafana \
  -n audityzer-prod
```

### Step 6: Deploy Application

```bash
# Build Docker image
docker build -t ghcr.io/romanchaa997/audityzer:1.0.0 .

# Push to registry
docker push ghcr.io/romanchaa997/audityzer:1.0.0

# Deploy to Kubernetes
kubectl set image deployment/audityzer-api \
  audityzer-api=ghcr.io/romanchaa997/audityzer:1.0.0 \
  -n audityzer-prod
```

## Configuration Files

### Docker Registry Setup
See `docker-registry-config.yml` for multi-registry configuration.

### Kubernetes Deployment
See `k8s-deployment.yml` for complete Kubernetes manifests.

### CI/CD Workflows
See `.github/workflows/` for GitHub Actions configuration.

## Verification Steps

```bash
# Check pod status
kubectl get pods -n audityzer-prod

# Check services
kubectl get svc -n audityzer-prod

# Check health
kubectl port-forward svc/audityzer-api 8080:80 -n audityzer-prod
curl http://localhost:8080/health
```

## Scaling Configuration

Horizontal scaling is configured in `k8s-deployment.yml`:
- Minimum replicas: 3
- Maximum replicas: 10
- CPU threshold: 70%
- Memory threshold: 80%

## Database Setup

```bash
# Create database
psql -h db.audityzer.internal -U postgres -c \
  "CREATE DATABASE audityzer_prod OWNER audityzer_user"

# Run migrations
python manage.py db upgrade
```

## Troubleshooting

### Pod not starting
```bash
kubectl describe pod <pod-name> -n audityzer-prod
kubectl logs <pod-name> -n audityzer-prod
```

### Network issues
```bash
kubectl exec -it <pod-name> -n audityzer-prod -- /bin/bash
telnet db.audityzer.internal 5432
```

## Maintenance

### Backup Terraform State
```bash
aws s3 cp s3://audityzer-terraform-state/prod/terraform.tfstate \
  terraform.tfstate.backup
```

### Update Infrastructure
```bash
terraform plan
terraform apply
```

## Security Best Practices

1. Never commit `.env` files
2. Rotate AWS credentials regularly
3. Use Kubernetes RBAC
4. Enable network policies
5. Use secrets management
6. Regular security scans

## Support

For issues or questions, refer to:
- DEPLOYMENT-GUIDE.md
- .github/workflows/ documentation
- AWS documentation
- Kubernetes official docs
