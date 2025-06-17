---
layout: default
title: "Deploy Minikube with Metrics"
date: 2025-06-14
categories: [kubernetes, minikube]
---

# Deploy Minikube with Metrics

This guide will walk you through setting up Minikube with metrics enabled and configuring a tunnel for external access.

## Prerequisites

- Minikube installed
- kubectl configured
- Docker installed
- Basic understanding of Kubernetes concepts

## Architecture Overview

<div class="mermaid">
graph TD
    subgraph "Local Machine"
        A[Docker] --> B[Minikube]
        B --> C[Kubernetes Components]
    end
    
    subgraph "Kubernetes Components"
        C --> D[Metrics Server]
        C --> E[Dashboard]
        C --> F[Ingress Controller]
    end
    
    subgraph "External Access"
        G[Minikube Tunnel] --> H[External Services]
        H --> I[Kubernetes Dashboard]
        H --> J[Ingress Routes]
    end
    
    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style G fill:#bfb,stroke:#333,stroke-width:2px
</div>

## Deployment Flow

<div class="mermaid">
flowchart TD
    A[Start Minikube] --> B[Enable Addons]
    B --> C[Start Tunnel]
    C --> D[Access Dashboard]
    D --> E[Verify Metrics]
    
    subgraph "Addons Setup"
        B1[Metrics Server] --> B
        B2[Dashboard] --> B
        B3[Ingress] --> B
    end
    
    subgraph "Verification Steps"
        E1[Check Nodes] --> E
        E2[Check Pods] --> E
        E3[Check Services] --> E
    end
    
    style A fill:#f9f,stroke:#333,stroke-width:2px
    style E fill:#bfb,stroke:#333,stroke-width:2px
</div>

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

## Step 3: Set Up Tunnel

```bash
# Start Minikube tunnel in a separate terminal
minikube tunnel

# Verify tunnel is working
kubectl get svc -A
```

## Step 4: Access the Dashboards

### Kubernetes Dashboard
```bash
# Start the dashboard
minikube dashboard
```

## Step 5: Verify Metrics Collection

```bash
# Check if metrics are being collected
kubectl top nodes
kubectl top pods -A
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

2. **Tunnel Issues**:
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

You've successfully set up Minikube with metrics enabled. This setup provides a good foundation for local development and testing.

## Next Steps

1. Deploy a monitoring stack
2. Configure custom dashboards
3. Set up alerting rules
4. Add more monitoring tools
5. Configure backup and restore
6. Set up CI/CD pipelines

Remember to regularly update your tools and review your configurations to ensure optimal performance and security. 

<div class="back-link">
  <a href="{{ site.baseurl }}/">← Back to Home</a>
</div>