# webapp Helm Chart

A production-ready Helm chart for deploying a generic web application to Kubernetes.

## Features

- **Deployment** with rolling update strategy (`maxUnavailable: 0`)
- **HorizontalPodAutoscaler** (CPU + memory metrics, autoscaling/v2)
- **PodDisruptionBudget** for zero-downtime node drains
- **Ingress** with TLS support
- **ServiceAccount** with `automountServiceAccountToken: false`
- **ConfigMap** for environment variable injection with checksum-based pod rollout
- **Pod anti-affinity** for HA across nodes
- **Security contexts** — non-root, drop ALL capabilities, seccomp RuntimeDefault
- **Liveness, readiness, and startup probes**

## Installing the Chart

```bash
helm repo add hermes-charts https://hermes-93.github.io/helm-charts
helm repo update

helm install my-app hermes-charts/webapp
```

## Uninstalling the Chart

```bash
helm uninstall my-app
```

## Parameters

### Image

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Container image | `nginx` |
| `image.tag` | Image tag (default: appVersion) | `""` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `imagePullSecrets` | Image pull secrets | `[]` |

### Deployment

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `2` |
| `podAnnotations` | Pod annotations | `{}` |
| `podLabels` | Extra pod labels | `{}` |
| `podSecurityContext` | Pod-level security context | see values.yaml |
| `securityContext` | Container security context | see values.yaml |

### Service

| Parameter | Description | Default |
|-----------|-------------|---------|
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `8080` |
| `service.annotations` | Service annotations | `{}` |

### Ingress

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable Ingress | `false` |
| `ingress.className` | Ingress class name | `""` |
| `ingress.annotations` | Ingress annotations | `{}` |
| `ingress.hosts` | Ingress hosts | see values.yaml |
| `ingress.tls` | Ingress TLS config | `[]` |

### Autoscaling (HPA)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `autoscaling.enabled` | Enable HPA | `false` |
| `autoscaling.minReplicas` | Minimum replicas | `2` |
| `autoscaling.maxReplicas` | Maximum replicas | `10` |
| `autoscaling.targetCPUUtilizationPercentage` | CPU target % | `70` |
| `autoscaling.targetMemoryUtilizationPercentage` | Memory target % | `80` |

### Pod Disruption Budget

| Parameter | Description | Default |
|-----------|-------------|---------|
| `podDisruptionBudget.enabled` | Enable PDB | `true` |
| `podDisruptionBudget.minAvailable` | Min available pods | `1` |

### Resources

| Parameter | Description | Default |
|-----------|-------------|---------|
| `resources.requests.cpu` | CPU request | `50m` |
| `resources.requests.memory` | Memory request | `64Mi` |
| `resources.limits.cpu` | CPU limit | `200m` |
| `resources.limits.memory` | Memory limit | `256Mi` |

### Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `config.env` | Environment variables injected via ConfigMap | `{}` |

## Example: Production deployment with Ingress + TLS + HPA

```yaml
image:
  repository: myapp
  tag: v2.1.0

replicaCount: 3

ingress:
  enabled: true
  className: nginx
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
  hosts:
    - host: myapp.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: myapp-tls
      hosts:
        - myapp.example.com

autoscaling:
  enabled: true
  minReplicas: 3
  maxReplicas: 20
  targetCPUUtilizationPercentage: 60

config:
  env:
    LOG_LEVEL: info
    PORT: "8080"
```

## Helm Tests

Run connectivity test after install:

```bash
helm test my-app
```

The test pod uses `busybox wget` to verify the Service is reachable.
