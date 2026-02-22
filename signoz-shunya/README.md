# SigNoz Deployment for Shunya

This directory contains the Helm chart configuration for deploying SigNoz on the Shunya EKS cluster.

## Structure

```
signoz-shunya/
├── Chart.yaml                          # Helm chart with dependencies
├── .helmignore                         # Helm ignore patterns
├── rbac.yaml                           # ClusterRole and ClusterRoleBinding for ClickHouse operator
├── clickhouseinstallation.yaml         # ClickHouse installation manifest
├── argocd-application.yaml             # ArgoCD Application definition
├── signoz/
│   └── custom-values.yaml              # SigNoz custom values (S3, resources, etc.)
└── k8s-infra-otel-signoz/
    └── values.yaml                     # K8s infrastructure monitoring values
```

## Prerequisites

1. Namespace `signoz` should exist or use ArgoCD's `CreateNamespace=true`
2. ClickHouse operator CRDs installed
3. IAM role for S3 cold storage: `arn:aws:iam::734910191031:role/signoz-clickhouse-s3-role`
4. S3 bucket: `signoz-cold-storage-734910191031`

## Deployment with ArgoCD

1. Update `argocd-application.yaml` with your Git repository URL
2. Apply the RBAC first (if not already applied):
   ```bash
   kubectl apply -f rbac.yaml
   ```
3. Apply the ArgoCD Application:
   ```bash
   kubectl apply -f argocd-application.yaml
   ```

## Manual Deployment

```bash
# Update dependencies
helm dependency update

# Install/Upgrade
helm upgrade --install signoz . \
  -f signoz/custom-values.yaml \
  -f k8s-infra-otel-signoz/values.yaml \
  -n signoz --create-namespace
```

## Configuration

### SigNoz (signoz/custom-values.yaml)
- Storage class: `gp3`
- ClickHouse with S3 cold storage
- Resource limits for dev environment

### K8s Infrastructure Monitoring (k8s-infra-otel-signoz/values.yaml)
- OTEL collector endpoint: `signoz-otel-collector.signoz.svc.cluster.local:4317`
- Resource limits for dev environment
