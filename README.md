# Audityzer CI/CD Templates & Infrastructure as Code

![Status](https://img.shields.io/badge/status-active-brightgreen)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

## 🚀 Overview

This repository contains production-ready CI/CD pipelines, Kubernetes manifests, Terraform Infrastructure as Code, and Helm charts for the Audityzer platform. It provides a complete infrastructure setup for deploying applications on AWS with Kubernetes, monitoring, auto-scaling, and GitOps workflows.

## 📋 Repository Structure

```
.
├── .github/
│   └── workflows/
│       ├── build-and-test.yml      # Build, test, and security scanning
│       ├── deploy.yml              # Production deployment pipeline
│       ├── security-scan.yml       # Trivy, Snyk, OWASP checks
│       └── monitoring.yml          # Health checks and metrics
├── ALB-ingress-controller.yaml     # AWS Application Load Balancer
├── argocd-app.yaml                 # GitOps deployment with ArgoCD
├── build-and-test.yml              # Standalone build workflow
├── docker-registry-config.yml      # Docker registry configuration
├── helm-values.yaml                # Helm chart values
├── k8s-deployment.yml              # Kubernetes deployment manifests
├── terraform-main.tf               # AWS infrastructure (IaC)
├── DEPLOYMENT-GUIDE.md             # Detailed deployment guide
├── INFRASTRUCTURE-SETUP.md         # Infrastructure setup guide
├── .env.example                    # Environment variables template
└── README.md                       # This file
```

## 🔑 Key Features

### CI/CD Pipelines
- **Automated Testing**: pytest, flake8 linting
- **Security Scanning**: Trivy, Snyk, OWASP Dependency-Check
- **Docker Builds**: Multi-architecture image builds (amd64, arm64, arm/v7)
- **Deployment Automation**: Staging and production deployments
- **Monitoring**: Prometheus metrics, health checks every 15 minutes

### Infrastructure as Code
- **AWS Infrastructure**: VPC, Subnets, NAT, IGW with Terraform
- **Kubernetes**: Deployment, Service, HPA (3-10 replicas)
- **Helm Charts**: Templated deployments with values
- **GitOps**: ArgoCD for continuous deployment
- **Load Balancer**: AWS ALB with SSL/TLS, health checks

### Observability
- **Prometheus**: Metrics collection and storage
- **Health Checks**: Liveness and readiness probes
- **Auto-scaling**: CPU/Memory-based horizontal pod autoscaling
- **Logging**: Structured logging and log analysis
- **Alerting**: Automated alerts on threshold breaches

## 🛠️ Quick Start

### Prerequisites
```bash
- Docker 24.x
- Kubernetes 1.28+
- Terraform 1.5+
- Helm 3.x
- kubectl configured
- AWS CLI v2.x
```

### Deploy with ArgoCD
```bash
# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Create Application
kubectl apply -f argocd-app.yaml

# Access ArgoCD UI
kubectl port-forward -n argocd svc/argocd-server 8080:443
```

### Deploy with Helm
```bash
helm install audityzer . -f helm-values.yaml -n audityzer-prod
```

### Deploy with Terraform
```bash
terraform init
terraform plan
terraform apply
```

## 📁 Configuration Files

| File | Purpose |
|------|--------|
| `.env.example` | Environment variables template |
| `helm-values.yaml` | Helm chart configuration |
| `k8s-deployment.yml` | Kubernetes manifests |
| `terraform-main.tf` | AWS infrastructure |
| `docker-registry-config.yml` | Docker registry settings |
| `ALB-ingress-controller.yaml` | AWS Load Balancer config |
| `argocd-app.yaml` | GitOps application definition |

## 🔄 CI/CD Workflows

### Build and Test
Triggered on: Push to main, Pull requests
```
Code Lint → Unit Tests → Docker Build → Security Scan → Coverage Upload
```

### Deploy
Triggered on: Push to main
```
Staging Deploy → Smoke Tests → Production Deploy → Deployment Records
```

### Security Scan
Triggered on: Weekly schedule, On-demand
```
Trivy Scan → Snyk Check → Dependency Check → Report Upload
```

### Monitoring
Triggered on: Every 15 minutes
```
Health Checks → Metrics Collection → Log Analysis → Performance Monitoring
```

## 📊 Monitoring & Metrics

- **Health Endpoints**: `/health`, `/readiness`
- **Metrics Endpoint**: `/metrics` (Prometheus)
- **Auto-scaling**: CPU 70%, Memory 80% thresholds
- **Replicas**: 3-10 pod replicas (configurable)
- **Health Check Interval**: 15 seconds

## 🔐 Security Features

- Non-root containers (UID 1000)
- Read-only filesystems
- Network policies
- RBAC configuration
- SSL/TLS termination
- Security scanning (Trivy, Snyk, OWASP)
- Secrets management with Kubernetes Secrets

## 📚 Documentation

- [DEPLOYMENT-GUIDE.md](./DEPLOYMENT-GUIDE.md) - Comprehensive deployment guide
- [INFRASTRUCTURE-SETUP.md](./INFRASTRUCTURE-SETUP.md) - Infrastructure setup instructions

## 🤝 Contributing

1. Create feature branch
2. Make changes
3. Run tests locally
4. Submit pull request

## 📝 License

MIT License - See LICENSE file

## 👨‍💻 Authors

- Roman Chaa (romanchaa997@gmail.com)

## 🔗 Links

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Kubernetes Documentation](https://kubernetes.io/docs)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Helm Documentation](https://helm.sh/docs)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io)

---

**Last Updated**: December 2025
**Status**: Production Ready ✅
