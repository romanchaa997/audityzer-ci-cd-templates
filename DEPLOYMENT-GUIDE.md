# Audityzer CI/CD Platform Deployment Guide

## Overview
This guide covers the complete deployment infrastructure for Audityzer platform using GitHub Actions CI/CD pipelines, Kubernetes, Prometheus monitoring, and Docker registries.

## Architecture Components

### 1. CI/CD Pipelines (.github/workflows/)

#### build-and-test.yml
- Runs on every push to main and pull requests
- Lints code with flake8
- Executes unit tests with pytest
- Uploads coverage to Codecov
- Builds Docker images
- Scans with security tools (bandit)

#### deploy.yml
- Triggered on pushes to main
- Deploys to staging environment
- Runs smoke tests for validation
- Deploys to production environment
- Creates deployment records in GitHub
- Uploads deployment logs

#### security-scan.yml
- Scheduled weekly or on-demand
- Trivy filesystem scanning (SAST)
- Snyk container security
- OWASP Dependency Check
- Uploads reports to GitHub Security tab

#### monitoring.yml
- Runs every 15 minutes
- Health checks on application endpoints
- Collects Prometheus metrics
- Log analysis for errors and warnings
- Performance metrics monitoring
- Alerts when thresholds exceeded

### 2. Monitoring Stack

#### Prometheus Configuration
- Global scrape interval: 15 seconds
- Targets: audityzer API, Kubernetes pods, Docker metrics, Node exporters
- Alert rules and recording rules support
- Integration with Alertmanager

#### Observability
- Prometheus for metrics
- Grafana for visualization
- ELK Stack for centralized logging
- Jaeger for distributed tracing

### 3. Docker Registry Configuration

**Supported Registries:**
- Docker Hub: ${DOCKER_USERNAME}:${DOCKER_PASSWORD}
- GitHub Container Registry (ghcr.io): ${GITHUB_TOKEN}
- Private Registry: Custom credentials

**Image Settings:**
- Default registry: ghcr.io
- Multi-arch builds: linux/amd64, linux/arm64, linux/arm/v7
- Image scanning: Trivy with HIGH severity threshold
- Auto-push on build enabled

## Deployment Steps

### Prerequisites
```bash
# Required tools
- Docker 20.10+
- Kubernetes 1.24+
- kubectl configured
- Git with GitHub CLI
```

### Environment Variables
Set the following secrets in GitHub:
- DOCKER_USERNAME, DOCKER_PASSWORD, DOCKER_EMAIL
- GITHUB_TOKEN
- REGISTRY_USERNAME, REGISTRY_PASSWORD
- SNYK_TOKEN
- MONITORING_WEBHOOK_URL

### Deployment Process

1. **Code Push**: Push to main branch
2. **Build & Test**: GitHub Actions runs build-and-test workflow
3. **Security Scan**: Automated security scanning and reporting
4. **Staging Deploy**: Deploy to staging environment
5. **Smoke Tests**: Validate deployment
6. **Production Deploy**: Deploy to production
7. **Monitoring**: Continuous monitoring and alerting

## Monitoring and Observability

### Prometheus Scrape Targets
```
audityzer-api: localhost:8080/metrics
docker: localhost:9323
node-exporter: localhost:9100
cadvisor: localhost:8080
```

### Health Endpoints
```
GET /health - Liveness probe
GET /readiness - Readiness probe
GET /metrics - Prometheus metrics
```

### Alert Thresholds
- CPU Usage > 80%
- Memory Usage > 85%
- Error Rate > 5%
- Response Time > 1000ms

## Rollback Procedures

1. Identify failed deployment
2. Review deployment logs
3. Execute rollback workflow
4. Verify application health
5. Post-incident review

## Best Practices

1. Always test in staging first
2. Use semantic versioning for releases
3. Monitor error rates post-deployment
4. Keep deployment logs for 90 days
5. Regular security scanning
6. Auto-scaling enabled for production

## Troubleshooting

### Deployment Failures
- Check GitHub Actions logs
- Review docker build output
- Verify registry credentials
- Check Kubernetes resource availability

### Monitoring Issues
- Verify Prometheus scrape targets
- Check alert rule syntax
- Review Alertmanager configuration
- Inspect metric collection

## References

- GitHub Actions: https://docs.github.com/en/actions
- Prometheus: https://prometheus.io/docs
- Kubernetes: https://kubernetes.io/docs
- Docker: https://docs.docker.com
