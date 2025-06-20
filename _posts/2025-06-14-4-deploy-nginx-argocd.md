---
layout: default
title: "Deploy Nginx Using ArgoCD"
date: 2025-06-14
categories: [kubernetes, nginx]
---

# Deploy Nginx Using ArgoCD

This guide will walk you through deploying Nginx using an existing ArgoCD installation, demonstrating GitOps principles in action.

## Prerequisites

- ArgoCD already installed and running
- Access to ArgoCD UI or CLI
- A Git repository for storing manifests
- kubectl configured to communicate with your cluster

## Step 1: Create Application Manifests

Create a new directory in your Git repository for the Nginx application:

```bash
mkdir -p nginx-app/base
cd nginx-app/base
```

Create the following manifest files:

### 1. Deployment (deployment.yaml)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: default
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 200m
            memory: 256Mi
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 20
```

### 2. Service (service.yaml)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: default
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  type: ClusterIP
```

### 3. Ingress (ingress.yaml)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
  namespace: default
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
  - hosts:
    - nginx.example.com
    secretName: nginx-tls
  rules:
  - host: nginx.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
```

## Step 2: Create ArgoCD Application

Create an ArgoCD application manifest (application.yaml):

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/your-username/your-repo.git
    targetRevision: HEAD
    path: nginx-app/base
  destination:
    server: https://kubernetes.default.svc
    namespace: default
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

## Step 3: Deploy the Application

### Using ArgoCD CLI

```bash
# Apply the application manifest
kubectl apply -f application.yaml

# Check application status
argocd app get nginx
```

### Using ArgoCD UI

1. Log in to ArgoCD UI
2. Click "New App"
3. Fill in the application details:
   - Name: nginx
   - Project: default
   - Repository URL: your Git repo URL
   - Path: nginx-app/base
   - Destination: your cluster
   - Namespace: default
4. Click "Create"

## Step 4: Verify Deployment

Check the deployment status:

```bash
# Check pods
kubectl get pods -l app=nginx

# Check service
kubectl get svc nginx

# Check ingress
kubectl get ingress nginx
```

## Step 5: Access the Application

Once the ingress is configured and DNS is set up, you can access the application at:
```
https://nginx.example.com
```

## Best Practices

1. **Version Control**:
   - Use semantic versioning for container images
   - Tag your manifests with versions
   - Use Git tags for releases

2. **Security**:
   - Use network policies
   - Implement proper RBAC
   - Use secrets for sensitive data
   - Enable TLS for ingress

3. **Monitoring**:
   - Set up proper health checks
   - Configure resource limits
   - Implement logging
   - Set up metrics collection

4. **High Availability**:
   - Use multiple replicas
   - Configure proper pod disruption budgets
   - Use anti-affinity rules

## Troubleshooting

Common issues and solutions:

1. **Sync Issues**:
   ```bash
   # Check application status
   argocd app get nginx
   
   # Check sync history
   argocd app history nginx
   ```

2. **Deployment Issues**:
   ```bash
   # Check pod status
   kubectl describe pod -l app=nginx
   
   # Check pod logs
   kubectl logs -l app=nginx
   ```

3. **Ingress Issues**:
   ```bash
   # Check ingress status
   kubectl describe ingress nginx
   
   # Check ingress controller logs
   kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx
   ```

## Conclusion

You've successfully deployed Nginx using ArgoCD, implementing GitOps principles. This setup provides a foundation for managing your applications in a declarative way.

## Next Steps

1. Set up additional applications
2. Configure CI/CD pipelines
3. Implement monitoring and alerting
4. Set up backup and disaster recovery

Remember to regularly update your applications and review your configurations to ensure optimal performance and security. 

<div class="back-link">
  <a href="{{ site.baseurl }}/">← Back to Home</a>
</div> 