# Setup Guide

Simple setup guide for the GitOps ngrok demo.

## Prerequisites

- Kubernetes cluster (1.19+)
- ArgoCD installed
- ngrok account with API key and authtoken
- kubectl configured for your cluster

## Step-by-Step Setup

### 1. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Configure ngrok Credentials

```bash
kubectl create namespace ngrok-operator
kubectl create secret generic ngrok-credentials \
  --from-literal=API_KEY=your_ngrok_api_key \
  --from-literal=AUTHTOKEN=your_ngrok_authtoken \
  -n ngrok-operator
```

### 3. Fork Repository (Optional)

This demo uses the public repository `https://github.com/ngrok-samples/gitops`. 

**If you want to make modifications:**
1. Fork this repository to your own GitHub account
2. Update the `repoURL` in these files with your fork's URL:
   - `argocd/app-of-apps.yaml`
   - `argocd/applications/infrastructure.yaml`
   - `argocd/applications/demo-app.yaml`

**For testing:** You can use the demo as-is without forking.

### 4. Deploy

```bash
kubectl apply -f argocd/app-of-apps.yaml
```

### 5. Access ArgoCD UI

```bash
# Get the admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Port forward to access UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Access ArgoCD at https://localhost:8080 with username `admin`.

## Verification

1. Check ArgoCD applications are synced
2. Verify pods are running:
   ```bash
   kubectl get pods -n ngrok-operator
   kubectl get pods -n demo-app
   ```
3. Check the ngrok endpoint:
   ```bash
   kubectl get agentendpoint -n demo-app
   ```
4. Access your application at `https://demo-app.prod.internal`

## Troubleshooting

### Common Issues

1. **ngrok operator not starting**: Check credentials secret
2. **Endpoint not ready**: Verify ngrok domain configuration
3. **ArgoCD sync issues**: Check repository permissions and URLs

### Useful Commands

```bash
# Check ArgoCD applications
kubectl get applications -n argocd

# View ngrok operator logs
kubectl logs -n ngrok-operator deployment/ngrok-operator

# Check ngrok endpoint status
kubectl describe agentendpoint demo-app-endpoint -n demo-app

# View application logs
kubectl logs -n demo-app deployment/demo-app
```
