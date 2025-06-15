---
layout: default
title: "Deploy Loki and Prometheus Using ArgoCD"
date: 2025-06-14
categories: [tutorial]
---

# Deploy Loki and Prometheus Using ArgoCD

This guide will walk you through deploying Loki and Prometheus using ArgoCD, setting up a complete monitoring stack.

## Prerequisites

- ArgoCD already installed and running
- Access to ArgoCD UI or CLI
- A Git repository for storing manifests
- kubectl configured to communicate with your cluster
- Helm repository access

## Step 1: Create Application Structure

Create the following directory structure in your Git repository:

```bash
monitoring/
├── base/
│   ├── prometheus/
│   │   └── values.yaml
│   └── loki/
│       └── values.yaml
└── applications/
    ├── prometheus-app.yaml
    └── loki-app.yaml
```

## Step 2: Configure Prometheus

Create `monitoring/base/prometheus/values.yaml`:

```yaml
# Prometheus configuration
server:
  persistentVolume:
    enabled: true
    size: 50Gi
  resources:
    requests:
      cpu: 500m
      memory: 1Gi
    limits:
      cpu: 1000m
      memory: 2Gi

alertmanager:
  enabled: true
  persistentVolume:
    enabled: true
    size: 10Gi
  resources:
    requests:
      cpu: 100m
      memory: 256Mi
    limits:
      cpu: 200m
      memory: 512Mi

pushgateway:
  enabled: true
  resources:
    requests:
      cpu: 50m
      memory: 128Mi
    limits:
      cpu: 100m
      memory: 256Mi

kubeStateMetrics:
  enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi

nodeExporter:
  enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi

grafana:
  enabled: true
  adminPassword: "admin"  # Change this in production
  persistentVolume:
    enabled: true
    size: 10Gi
  resources:
    requests:
      cpu: 100m
      memory: 256Mi
    limits:
      cpu: 200m
      memory: 512Mi
```

## Step 3: Configure Loki

Create `monitoring/base/loki/values.yaml`:

```yaml
# Loki configuration
loki:
  auth_enabled: false
  storage:
    type: filesystem
  persistence:
    enabled: true
    size: 50Gi
  resources:
    requests:
      cpu: 500m
      memory: 1Gi
    limits:
      cpu: 1000m
      memory: 2Gi

promtail:
  enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 256Mi
    limits:
      cpu: 200m
      memory: 512Mi

grafana:
  enabled: true
  sidecar:
    datasources:
      enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 256Mi
    limits:
      cpu: 200m
      memory: 512Mi
```

## Step 4: Create ArgoCD Applications

Create `monitoring/applications/prometheus-app.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: prometheus
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://prometheus-community.github.io/helm-charts
    targetRevision: "*"
    chart: prometheus
    helm:
      releaseName: prometheus
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: monitoring
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
    retry:
      limit: 5
      backoff:
        duration: 5s
        factor: 2
        maxDuration: 3m
```

Create `monitoring/applications/loki-app.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: loki
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://grafana.github.io/helm-charts
    targetRevision: "*"
    chart: loki-stack
    helm:
      releaseName: loki
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: monitoring
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
    retry:
      limit: 5
      backoff:
        duration: 5s
        factor: 2
        maxDuration: 3m
```

## Step 5: Deploy the Applications

### Using ArgoCD CLI

```bash
# Apply the application manifests
kubectl apply -f monitoring/applications/prometheus-app.yaml
kubectl apply -f monitoring/applications/loki-app.yaml

# Check application status
argocd app get prometheus
argocd app get loki
```

### Using ArgoCD UI

1. Log in to ArgoCD UI
2. Click "New App"
3. For Prometheus:
   - Name: prometheus
   - Project: default
   - Repository URL: https://prometheus-community.github.io/helm-charts
   - Chart: prometheus
   - Version: *
   - Destination: your cluster
   - Namespace: monitoring
4. Repeat for Loki with appropriate values

## Step 6: Verify Deployment

Check the deployment status:

```bash
# Check Prometheus pods
kubectl get pods -n monitoring -l app=prometheus

# Check Loki pods
kubectl get pods -n monitoring -l app=loki

# Check Grafana
kubectl get pods -n monitoring -l app=grafana
```

## Step 7: Access the Dashboards

### Prometheus
```bash
kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
```
Access at: http://localhost:9090

### Grafana
```bash
kubectl port-forward svc/prometheus-grafana -n monitoring 3000:80
```
Access at: http://localhost:3000
Default credentials:
- Username: admin
- Password: admin (as set in values.yaml)

## Best Practices

1. **Resource Management**:
   - Set appropriate resource limits
   - Monitor storage usage
   - Configure retention policies

2. **Security**:
   - Enable authentication
   - Use network policies
   - Secure sensitive data
   - Enable TLS

3. **High Availability**:
   - Use persistent storage
   - Configure proper health checks
   - Set up proper backup

4. **Monitoring**:
   - Set up alerts
   - Configure dashboards
   - Monitor the monitoring stack

## Troubleshooting

Common issues and solutions:

1. **Prometheus Issues**:
   ```bash
   # Check Prometheus logs
   kubectl logs -n monitoring -l app=prometheus-server
   
   # Check Prometheus targets
   kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
   # Then visit http://localhost:9090/targets
   ```

2. **Loki Issues**:
   ```bash
   # Check Loki logs
   kubectl logs -n monitoring -l app=loki
   
   # Check Promtail logs
   kubectl logs -n monitoring -l app=promtail
   ```

3. **Grafana Issues**:
   ```bash
   # Check Grafana logs
   kubectl logs -n monitoring -l app=grafana
   
   # Check datasource configuration
   kubectl get configmap -n monitoring -l grafana_datasource
   ```

## Conclusion

You've successfully deployed Loki and Prometheus using ArgoCD, setting up a complete monitoring stack. This setup provides a foundation for monitoring your Kubernetes cluster and applications.

## Next Steps

1. Configure custom dashboards
2. Set up alerting rules
3. Configure log retention policies
4. Set up backup and disaster recovery
5. Implement additional monitoring tools

Remember to regularly update your monitoring stack and review your configurations to ensure optimal performance and security. 

<div class="back-link">
  <a href="{{ site.baseurl }}/">← Back to Home</a>
</div>

<style>
body {
  background-color: #1a1a1a;
  color: white;
}

.page-header {
  max-width: none !important;
  padding: 2rem 6rem !important;
}

.project-name {
  margin-bottom: 1.5rem !important;
}

.project-tagline {
  margin-top: 2rem !important;
  margin-bottom: 2rem !important;
  font-size: 1.5rem !important;
  line-height: 1.6 !important;
}

.main-content {
  max-width: none !important;
  padding: 2rem 6rem !important;
}

h1, h2, h3 {
  color: white;
}

h1 {
  font-size: 2.5em;
  margin-bottom: 0.5em;
  font-weight: 600;
}

h2 {
  margin: 1.5em 0 1em 0;
  border-bottom: 2px solid #333;
  padding-bottom: 0.5em;
  font-size: 1.8em;
}

h3 {
  margin: 1em 0 0.5em 0;
  font-size: 1.4em;
}

p {
  line-height: 1.6;
  color: #e0e0e0;
}

ul, ol {
  color: #e0e0e0;
  line-height: 1.6;
}

li {
  margin-bottom: 0.5em;
}

strong {
  color: white;
}

a {
  color: #66b3ff;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

pre {
  background-color: #2a2a2a;
  border-radius: 3px;
  padding: 16px;
  overflow: auto;
  color: #e0e0e0;
}

code {
  font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, Courier, monospace;
  font-size: 0.9em;
  color: #e0e0e0;
}

.back-link {
  margin: 2em 0;
  padding: 0.5em 0;
}

.back-link a {
  color: #66b3ff;
  font-size: 0.9em;
}

.back-link a:hover {
  color: #99ccff;
}

@media (max-width: 768px) {
  .page-header {
    padding: 1rem 2rem !important;
  }

  .main-content {
    padding: 1rem 2rem !important;
  }

  h1 {
    font-size: 2em;
  }

  h2 {
    font-size: 1.6em;
  }

  h3 {
    font-size: 1.2em;
  }
}
</style> 