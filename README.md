# GitOps Workflow using ArgoCD and Kubernetes

## Overview
This project demonstrates GitOps-based continuous deployment using ArgoCD and Kubernetes. Kubernetes manifests are stored in GitHub, and ArgoCD continuously monitors the repository and maintains the desired application state.

## Architecture
GitHub Repository → ArgoCD → Kind Kubernetes Cluster → NGINX

## Technologies Used
- Fedora Linux
- Docker
- Kind
- Kubernetes
- ArgoCD
- Git and GitHub
- NGINX

## Project Structure
```text
argocd-gitops-project/
├── argocd/
│   └── application.yaml
├── kubernetes/
│   ├── deployment.yaml
│   └── service.yaml
├── screenshots/
└── README.md
