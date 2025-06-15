---
layout: post
title: "Deploy Minikube with Metrics and Tunnel"
date: 2024-03-21
categories: kubernetes monitoring
---

# Deploy Minikube with Metrics and Tunnel

This guide will walk you through setting up Minikube with metrics enabled and configuring a tunnel for external access.

## Prerequisites

- Minikube installed
- kubectl configured
- Docker installed
- Basic understanding of Kubernetes concepts

## Step 1: Start Minikube with Metrics

```bash
# Start Minikube with metrics server enabled
minikube start \
  --addons=metrics-server \
  --memory=4096 \
  --cpus=2 \
  --driver=docker

# Verify metrics server is running
kubectl get pods -n kube-system | grep metrics-server
```

## Step 2: Enable Minikube Addons

```bash
# Enable required addons
minikube addons enable metrics-server
minikube addons enable dashboard
minikube addons enable ingress

# Verify addons status
minikube addons list
```

## Step 3: Deploy Prometheus and Loki

Create a `monitoring` directory and add the following files:

### 1. Prometheus Values (prometheus-values.yaml)

```yaml
server:
  persistentVolume:
    enabled: true
    size: 10Gi
  resources:
    requests:
      cpu: 200m
      memory: 512Mi
    limits:
      cpu: 500m
      memory: 1Gi

alertmanager:
  enabled: false

pushgateway:
  enabled: false

kubeStateMetrics:
  enabled: true
  resources:
    requests:
      cpu: 50m
      memory: 64Mi
    limits:
      cpu: 100m
      memory: 128Mi

nodeExporter:
  enabled: true
  resources:
    requests:
      cpu: 50m
      memory: 64Mi
    limits:
      cpu: 100m
      memory: 128Mi

grafana:
  enabled: true
  adminPassword: "admin"
  persistentVolume:
    enabled: true
    size: 5Gi
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi
```

### 2. Loki Values (loki-values.yaml)

```yaml
loki:
  auth_enabled: false
  storage:
    type: filesystem
  persistence:
    enabled: true
    size: 10Gi
  resources:
    requests:
      cpu: 200m
      memory: 512Mi
    limits:
      cpu: 500m
      memory: 1Gi

promtail:
  enabled: true
  resources:
    requests:
      cpu: 50m
      memory: 64Mi
    limits:
      cpu: 100m
      memory: 128Mi

grafana:
  enabled: true
  sidecar:
    datasources:
      enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi
```

## Step 4: Install Prometheus and Loki

```bash
# Add Helm repositories
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Create monitoring namespace
kubectl create namespace monitoring

# Install Prometheus
helm install prometheus prometheus-community/prometheus \
  --namespace monitoring \
  --values prometheus-values.yaml

# Install Loki
helm install loki grafana/loki-stack \
  --namespace monitoring \
  --values loki-values.yaml
```

## Step 5: Set Up Tunnel

```bash
# Start Minikube tunnel in a separate terminal
minikube tunnel

# Verify tunnel is working
kubectl get svc -A
```

## Step 6: Access the Dashboards

### Kubernetes Dashboard
```bash
# Start the dashboard
minikube dashboard
```

### Prometheus
```bash
# Port forward Prometheus
kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
```
Access at: http://localhost:9090

### Grafana
```bash
# Port forward Grafana
kubectl port-forward svc/prometheus-grafana -n monitoring 3000:80
```
Access at: http://localhost:3000
Default credentials:
- Username: admin
- Password: admin

## Step 7: Verify Metrics Collection

```bash
# Check if metrics are being collected
kubectl top nodes
kubectl top pods -A

# Check Prometheus targets
kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
# Then visit http://localhost:9090/targets
```

## Troubleshooting

Common issues and solutions:

1. **Metrics Server Issues**:
   ```bash
   # Check metrics server logs
   kubectl logs -n kube-system -l k8s-app=metrics-server
   
   # Verify metrics server is running
   kubectl get pods -n kube-system | grep metrics-server
   ```

2. **Prometheus Issues**:
   ```bash
   # Check Prometheus pods
   kubectl get pods -n monitoring -l app=prometheus-server
   
   # Check Prometheus logs
   kubectl logs -n monitoring -l app=prometheus-server
   ```

3. **Loki Issues**:
   ```bash
   # Check Loki pods
   kubectl get pods -n monitoring -l app=loki
   
   # Check Loki logs
   kubectl logs -n monitoring -l app=loki
   ```

4. **Tunnel Issues**:
   ```bash
   # Check if tunnel is running
   ps aux | grep "minikube tunnel"
   
   # Restart tunnel if needed
   pkill -f "minikube tunnel"
   minikube tunnel
   ```

## Best Practices

1. **Resource Management**:
   - Monitor resource usage
   - Adjust resource limits as needed
   - Clean up unused resources

2. **Security**:
   - Change default passwords
   - Use network policies
   - Enable authentication

3. **Maintenance**:
   - Regularly update Minikube
   - Clean up old resources
   - Monitor storage usage

## Conclusion

You've successfully set up Minikube with metrics enabled and deployed a monitoring stack. This setup provides a good foundation for local development and testing.

## Next Steps

1. Configure custom dashboards
2. Set up alerting rules
3. Add more monitoring tools
4. Configure backup and restore
5. Set up CI/CD pipelines

Remember to regularly update your tools and review your configurations to ensure optimal performance and security. 

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