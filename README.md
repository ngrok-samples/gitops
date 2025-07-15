# GitOps with ArgoCD and ngrok Kubernetes Operator

A simplified demonstration of how to automate ingress using the ngrok Kubernetes Operator with ArgoCD for GitOps workflows.

## Repository Structure

```
├── argocd/                    # ArgoCD application definitions
│   ├── app-of-apps.yaml      # App of apps pattern
│   └── applications/         # Individual application manifests
├── apps/                     # Application manifests
│   └── demo-app/            # Simple web application
├── infrastructure/          # Infrastructure components
│   ├── ngrok-operator/     # ngrok Operator installation
│   └── namespaces/         # Namespace definitions
```

## Quick Start

1. **Install ArgoCD** in your cluster
2. **Install ngrok Operator** using the manifests in `infrastructure/ngrok-operator/`
3. **Configure your ngrok credentials** as described below
4. **Apply the app-of-apps** pattern: `kubectl apply -f argocd/app-of-apps.yaml`

## ngrok Configuration

Before deploying, you'll need to:

1. Create an ngrok account and obtain an API key
2. Create a Kubernetes secret with your ngrok credentials:

```bash
kubectl create secret generic ngrok-credentials \
  --from-literal=API_KEY=your_api_key \
  --from-literal=AUTHTOKEN=your_authtoken \
  -n ngrok-operator
```

## What This Demonstrates

- **GitOps Workflow**: All deployments managed through Git
- **ngrok CRDs**: Direct use of ngrok Custom Resources for internal endpoints
- **App of Apps Pattern**: Centralized management of multiple applications
- **Internal URL**: Application accessible at `https://demo-app.prod.internal`

## Components

- **Demo Application**: Simple nginx web service with custom HTML
- **ngrok Operator**: Kubernetes operator for managing ngrok tunnels
- **ArgoCD Applications**: Declarative application management using plain YAML manifests

## Usage

Once deployed, ArgoCD will automatically sync the applications. Any changes to this repository will trigger updates to the cluster.

Access your application at: `https://demo-app.prod.internal`
