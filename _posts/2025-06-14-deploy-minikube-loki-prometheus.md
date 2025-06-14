---
layout: default
title: "Deploying Minikube with Loki and Prometheus"
date: 2025-06-14
categories: [tutorial]
---



# Deploying Minikube with Loki and Prometheus

This guide will walk you through setting up a local Kubernetes environment with Minikube, and deploying Loki for log aggregation and Prometheus for metrics monitoring.

## Prerequisites

- Docker installed
- Minikube installed
- kubectl installed
- Helm installed

## Step 1: Start Minikube

```bash
# Start Minikube with enough resources
minikube start --memory=4096 --cpus=2 --driver=docker

# Verify the cluster is running
kubectl get nodes
```

## Step 2: Install Prometheus using Helm

```bash
# Add Prometheus Helm repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install Prometheus
helm install prometheus prometheus-community/kube-prometheus-stack
```

## Step 3: Install Loki using Helm

```bash
# Add Grafana Helm repository (includes Loki)
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install Loki
helm install loki grafana/loki-stack
```

## Step 4: Configure Data Persistence

Create a `loki-pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: loki-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

Apply the PVC:
```bash
kubectl apply -f loki-pvc.yaml
```

## Step 5: Access the Dashboards

### Prometheus Dashboard
```bash
# Port forward Prometheus
kubectl port-forward svc/prometheus-operated 9090:9090
```
Access Prometheus at: http://localhost:9090

### Grafana Dashboard
```bash
# Port forward Grafana
kubectl port-forward svc/prometheus-grafana 3000:80
```
Access Grafana at: http://localhost:3000
Default credentials:
- Username: admin
- Password: prom-operator

## Step 6: Configure Data Sources

1. Log into Grafana
2. Add Prometheus as a data source:
   - URL: http://prometheus-operated:9090
   - Access: Server (default)

3. Add Loki as a data source:
   - URL: http://loki:3100
   - Access: Server (default)

## Step 7: Create Example Dashboards

### Prometheus Dashboard
Create a new dashboard with:
- CPU Usage
- Memory Usage
- Pod Status
- Node Status

### Loki Dashboard
Create a new dashboard with:
- Container Logs
- Error Rates
- Log Patterns

## Troubleshooting

Common issues and solutions:

1. **Prometheus not collecting metrics**
   ```bash
   kubectl get pods -n monitoring
   kubectl logs -n monitoring <prometheus-pod-name>
   ```

2. **Loki not receiving logs**
   ```bash
   kubectl get pods -n monitoring
   kubectl logs -n monitoring <loki-pod-name>
   ```

3. **Storage issues**
   ```bash
   kubectl get pvc
   kubectl describe pvc loki-pvc
   ```

## Best Practices

1. **Resource Management**
   - Set appropriate resource limits
   - Monitor storage usage
   - Regular cleanup of old data

2. **Security**
   - Use RBAC
   - Enable network policies
   - Regular updates

3. **Monitoring**
   - Set up alerts
   - Regular health checks
   - Backup configurations

## Next Steps

1. Set up alerting rules
2. Configure log retention policies
3. Create custom dashboards
4. Set up automated backups

<div class="back-link">
  <a href="{{ site.baseurl }}/">← Back to Home</a>
</div>

<style>
pre {
  background-color: #f6f8fa;
  border-radius: 3px;
  padding: 16px;
  overflow: auto;
}

code {
  font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, Courier, monospace;
  font-size: 0.9em;
}

.yaml {
  color: #032f62;
}

.bash {
  color: #24292e;
}
</style> 