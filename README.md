# Whoami Helm Chart

A Helm chart for deploying the [traefik/whoami](https://github.com/traefik/whoami) application on Kubernetes.

## Description

This chart deploys a simple web service that returns information about the HTTP request it receives. It's useful for testing and debugging purposes.

## Installation

### Install from OCI registry

```bash
helm install whoami oci://ghcr.io/anhkhoa289/whoami-chart/whoami --version 0.0.1
```

### Install from local directory

```bash
helm install whoami ./whoami
```

### Install with custom values

```bash
helm install whoami ./whoami -f custom-values.yaml
```

### Install with command-line overrides

```bash
helm install whoami ./whoami --set replicaCount=2 --set service.type=LoadBalancer
```

## Configuration

The following table lists the configurable parameters of the Whoami chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | Image repository | `traefik/whoami` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `image.tag` | Image tag | `""` (uses appVersion) |
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `80` |
| `ingress.enabled` | Enable ingress | `false` |
| `ingress.className` | Ingress class name | `""` |
| `ingress.hosts` | Ingress hosts configuration | `[{host: whoami.local, paths: [{path: /, pathType: Prefix}]}]` |
| `resources` | CPU/Memory resource requests/limits | `{}` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `autoscaling.minReplicas` | Minimum replicas for HPA | `1` |
| `autoscaling.maxReplicas` | Maximum replicas for HPA | `100` |

## Usage

After installation, you can access the application using:

### With ClusterIP service (default)

```bash
export POD_NAME=$(kubectl get pods -l "app.kubernetes.io/name=whoami" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:80
curl http://localhost:8080
```

### With LoadBalancer service

```bash
helm install whoami ./whoami --set service.type=LoadBalancer
# Wait for external IP
kubectl get svc whoami
```

### With Ingress

```bash
helm install whoami ./whoami --set ingress.enabled=true --set ingress.hosts[0].host=whoami.example.com
```

## Uninstall

```bash
helm uninstall whoami
```

## Chart Structure

```
whoami/
├── Chart.yaml              # Chart metadata
├── values.yaml             # Default configuration values
├── templates/
│   ├── deployment.yaml     # Deployment manifest
│   ├── service.yaml        # Service manifest
│   ├── serviceaccount.yaml # ServiceAccount manifest
│   ├── ingress.yaml        # Ingress manifest
│   ├── hpa.yaml           # HorizontalPodAutoscaler manifest
│   ├── _helpers.tpl       # Template helpers
│   └── NOTES.txt          # Post-installation notes
└── .helmignore            # Patterns to ignore when packaging
```