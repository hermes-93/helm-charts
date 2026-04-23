# Contributing

## Setup

```bash
git clone https://github.com/hermes-93/helm-charts.git
cd helm-charts

# Install Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Install chart-testing
pip install yamllint
```

## Adding a New Chart

1. Create the chart directory: `charts/<name>/`
2. Add `Chart.yaml`, `values.yaml`, `templates/`
3. Add CI test values: `charts/<name>/ci/default-values.yaml`
4. Add `charts/<name>/README.md`
5. Bump `version` in `Chart.yaml` for every change

## Testing Locally

```bash
# Lint
ct lint --config .ct.yaml

# Full install test with kind
kind create cluster --name helm-test
ct install --config .ct.yaml
kind delete cluster --name helm-test
```

## Commit Guidelines

- `feat(chartname): description` for new features
- `fix(chartname): description` for bug fixes
- `docs(chartname): description` for docs
- Always bump `Chart.yaml` version on any chart change

## Release Process

Charts are released automatically via `helm/chart-releaser-action` when changes to `charts/**` are merged to `main`. The chart index is published to GitHub Pages and usable as a Helm repository.
