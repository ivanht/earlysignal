---
layout: post
title: "Deploy ArgoCD Using Helm"
date: 2025-06-14
categories: [tutorial]
---

# Deploy ArgoCD Using Helm

This guide will walk you through deploying ArgoCD using Helm, including customizations and best practices.

## Prerequisites

- A running Kubernetes cluster
- kubectl configured to communicate with your cluster
- Helm v3 installed

## Step 1: Add ArgoCD Helm Repository

```bash
# Add ArgoCD Helm repository
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```

## Step 2: Create a Custom Values File

Create a `values.yaml` file with your custom configurations:

```yaml
# values.yaml
server:
  extraArgs:
    - --insecure
  ingress:
    enabled: true
    hosts:
      - argocd.local
    annotations:
      kubernetes.io/ingress.class: nginx
      cert-manager.io/cluster-issuer: letsencrypt-prod
    tls:
      - secretName: argocd-server-tls
        hosts:
          - argocd.local

  config:
    url: https://argocd.local
    admin.enabled: true

  rbac:
    policy.default: role:readonly
    policy.csv: |
      p, role:org-admin, applications, *, */*, allow
      p, role:org-admin, clusters, get, *, allow
      p, role:org-admin, projects, get, *, allow

  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi

redis:
  enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi

repoServer:
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi

applicationSet:
  enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 200m
      memory: 256Mi
```

## Step 3: Create Namespace and Install ArgoCD

```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD with custom values
helm install argocd argo/argocd \
  --namespace argocd \
  --values values.yaml \
  --wait
```

## Step 4: Access ArgoCD

Get the initial admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Access the UI:
- If using port-forward:
  ```bash
  kubectl port-forward svc/argocd-server -n argocd 8080:443
  ```
- Then visit https://localhost:8080

## Step 5: Configure Additional Settings

### Enable SSO (Optional)

Add these configurations to your `values.yaml`:

```yaml
server:
  config:
    oidc.config: |
      name: Okta
      issuer: https://your-okta-domain/oauth2/default
      clientID: your-client-id
      clientSecret: your-client-secret
      requestedScopes: ["openid", "profile", "email", "groups"]
```

### Configure Notifications (Optional)

```yaml
notifications:
  enabled: true
  secret:
    create: true
    annotations: {}
    labels: {}
  cm:
    create: true
    data:
      service.slack: |
        token: xoxb-your-token
        channel: your-channel
```

## Step 6: Upgrade ArgoCD

When you need to update ArgoCD:

```bash
helm upgrade argocd argo/argocd \
  --namespace argocd \
  --values values.yaml \
  --wait
```

## Best Practices

1. **Resource Management**:
   - Set appropriate resource limits
   - Monitor resource usage
   - Scale components as needed

2. **Security**:
   - Use RBAC policies
   - Enable SSO when possible
   - Keep ArgoCD updated
   - Use TLS for all connections

3. **High Availability**:
   - Deploy multiple replicas
   - Use persistent storage
   - Configure proper health checks

4. **Monitoring**:
   - Set up Prometheus metrics
   - Configure alerts
   - Monitor sync status

## Troubleshooting

Common issues and solutions:

1. **Installation Issues**:
   ```bash
   # Check pod status
   kubectl get pods -n argocd
   
   # Check pod logs
   kubectl logs -n argocd -l app.kubernetes.io/name=argocd-server
   ```

2. **Sync Issues**:
   - Verify Git repository access
   - Check application manifests
   - Review RBAC permissions

3. **Performance Issues**:
   - Check resource usage
   - Review number of applications
   - Monitor Redis performance

## Conclusion

You've successfully deployed ArgoCD using Helm with custom configurations. This setup provides a solid foundation for managing your Kubernetes applications using GitOps principles.

## Next Steps

1. Set up additional applications
2. Configure CI/CD pipelines
3. Implement monitoring and alerting
4. Set up backup and disaster recovery

Remember to regularly update ArgoCD and review your configurations to ensure optimal performance and security. 

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