# GitOps Workflow using ArgoCD and Kubernetes

## Overview
Automated Kubernetes deployment using GitOps principles.

## Architecture
GitHub → ArgoCD → Kubernetes → NGINX

## Technologies
- Fedora Linux
- Docker
- Minikube
- Kubernetes
- ArgoCD
- GitHub
- NGINX

## Kubernetes Resources
- Deployment
- Service
- Readiness Probe
- Liveness Probe

## GitOps Features
- Automated synchronization
- Self-healing
- Automatic pruning
- Git-based configuration management

## Deployment Workflow
1. Push Kubernetes manifests to GitHub.
2. ArgoCD monitors the Git repository.
3. ArgoCD detects configuration changes.
4. ArgoCD synchronizes Kubernetes resources.
5. Kubernetes runs the desired application state.

## Testing
Update the NGINX image version in deployment.yaml
and push the change to GitHub.
Observe automatic synchronization in ArgoCD.

