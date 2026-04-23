# Helm Charts

[![Lint and Test](https://github.com/hermes-93/helm-charts/actions/workflows/lint-test.yml/badge.svg)](https://github.com/hermes-93/helm-charts/actions/workflows/lint-test.yml)
[![Release Charts](https://github.com/hermes-93/helm-charts/actions/workflows/release.yml/badge.svg)](https://github.com/hermes-93/helm-charts/actions/workflows/release.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Production-quality Helm charts for Kubernetes. Tested with [chart-testing](https://github.com/helm/chart-testing) on every PR using a live `kind` cluster.

## Charts

| Chart | Description | Version |
|-------|-------------|---------|
| [webapp](charts/webapp/README.md) | Generic web application — Deployment, Service, Ingress, HPA, PDB, RBAC | 1.0.0 |

## Usage

### Add the Helm repository

```bash
helm repo add hermes-charts https://hermes-93.github.io/helm-charts
helm repo update
```

### Install the webapp chart

```bash
helm install my-app hermes-charts/webapp \
  --set image.repository=nginx \
  --set image.tag=alpine \
  --set ingress.enabled=true \
  --set ingress.hosts[0].host=myapp.example.com
```

### Install from OCI (GHCR)

```bash
helm install my-app oci://ghcr.io/hermes-93/helm-charts/webapp --version 1.0.0
```

## Development

```bash
# Lint all charts
ct lint --config .ct.yaml

# Install/test with kind
kind create cluster --name helm-test
ct install --config .ct.yaml
kind delete cluster --name helm-test
```

## CI Pipeline

```
pull_request
      │
      ▼
 ┌──────────┐
 │ ct lint  │  Validates chart structure, values schema, metadata
 └────┬─────┘
      │
      ▼
 ┌─────────────┐
 │ ct install  │  Deploys chart to ephemeral kind cluster, runs Helm tests
 └─────────────┘

push to main
      │
      ▼
 ┌──────────────────┐
 │ chart-releaser   │  Packages chart, publishes to GitHub Pages
 └──────────────────┘
```

## License

MIT
