# GitOps Workflow Using ArgoCD and Kubernetes

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![NGINX](https://img.shields.io/badge/NGINX-009639?style=for-the-badge&logo=nginx&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)

## Overview

This project demonstrates the implementation of a **GitOps-based continuous deployment workflow** using Kubernetes and ArgoCD.

The project deploys an NGINX application on a Kubernetes cluster using declarative Kubernetes manifests stored in a GitHub repository.

ArgoCD continuously monitors the Git repository and automatically synchronizes changes with the Kubernetes cluster.

The project also demonstrates **self-healing**, where ArgoCD detects manual changes made directly to the Kubernetes cluster and restores the desired state defined in Git.

## Project Objectives

- Understand GitOps principles and workflows.
- Deploy applications using Kubernetes manifests.
- Integrate ArgoCD with a GitHub repository.
- Implement automated synchronization.
- Configure self-healing using ArgoCD.
- Understand declarative infrastructure management.
- Monitor application health and deployment status.

## Architecture

```text
             Developer
                 |
                 |
          Push Changes
                 |
                 v
          GitHub Repository
                 |
                 |
          ArgoCD Monitors
                 |
                 v
          ArgoCD Controller
                 |
                 |
       Automatic Synchronization
                 |
                 v
          Kubernetes Cluster
                 |
                 v
          NGINX Deployment
                 |
          -----------------
          |       |       |
         Pod 1   Pod 2   Pod 3
          |       |       |
          -----------------
                 |
                 v
           NGINX Service
                 |
                 v
            Application
```

### How It Works

1. Kubernetes manifests are maintained in GitHub.
2. ArgoCD continuously monitors the configured repository.
3. When a change is pushed to GitHub, ArgoCD detects the new revision.
4. ArgoCD automatically synchronizes the desired configuration with Kubernetes.
5. Kubernetes creates or updates the required resources.
6. ArgoCD monitors application health and synchronization status.
7. If someone manually changes the cluster configuration, ArgoCD self-healing restores the desired state from Git.

## Technologies Used

| Technology | Purpose |
|---|---|
| Fedora Linux | Development environment |
| Docker | Container runtime |
| Kind | Local Kubernetes cluster |
| Kubernetes | Container orchestration |
| ArgoCD | GitOps continuous delivery |
| Git | Version control |
| GitHub | Source code and manifest repository |
| NGINX | Deployed web application |
| kubectl | Kubernetes cluster management |

## Project Structure

```text
argocd-gitops-project/
│
├── argocd/
│   └── application.yaml
│
├── kubernetes/
│   ├── deployment.yaml
│   └── service.yaml
│
├── screenshots/
│   ├── argocd-dashboard.png
│   ├── kubernetes-pods.png
│   ├── nginx-webpage.png
│   └── self-healing.png
│
└── README.md
```

## Kubernetes Resources

### 1. Deployment

The Kubernetes Deployment manages the NGINX application.

Features include:

- Three NGINX replicas.
- Container image: `nginx:1.27-alpine`.
- Readiness probe.
- Liveness probe.
- CPU and memory resource limits.

### 2. Service

A Kubernetes NodePort Service exposes the NGINX application within the cluster.

- Service name: `nginx-service`
- Service type: `NodePort`
- Service port: `80`
- NodePort: `30080`

For local browser access, the project uses Kubernetes port forwarding.

### 3. ArgoCD Application

The ArgoCD Application connects the GitHub repository to the Kubernetes cluster.

| Configuration | Value |
|---|---|
| Application name | `nginx-gitops` |
| Repository | `https://github.com/RAkSHITH256/argocd-gitops-project.git` |
| Branch | `main` |
| Manifest path | `kubernetes` |
| Destination namespace | `default` |
| Automated sync | Enabled |
| Self-healing | Enabled |
| Pruning | Enabled |

## GitOps Features Implemented

### 1. Automated Synchronization

ArgoCD monitors the GitHub repository for configuration changes.

When Kubernetes manifests are updated and pushed to GitHub, ArgoCD automatically synchronizes the changes with the cluster.

### 2. Self-Healing

Self-healing ensures that the actual Kubernetes cluster state matches the desired state stored in Git.

If someone manually modifies a resource, ArgoCD detects the difference and restores the expected configuration.

### 3. Automatic Pruning

When a resource managed by ArgoCD is removed from Git, automatic pruning allows ArgoCD to remove the corresponding resource from the cluster during synchronization.

### 4. Declarative Configuration

The Kubernetes infrastructure is defined using YAML manifests.

This makes the deployment reproducible, version-controlled, and easier to maintain.

## Setup and Deployment

### Prerequisites

Install and configure:

- Docker
- kubectl
- Kind
- Git
- Kubernetes cluster
- ArgoCD

### Step 1: Clone the Repository

```bash
git clone https://github.com/RAkSHITH256/argocd-gitops-project.git

cd argocd-gitops-project
```

### Step 2: Verify Kubernetes Cluster

```bash
kubectl config current-context

kubectl get nodes
```

The project was tested using the Kind cluster context:

```text
kind-ca-scheduler
```

### Step 3: Install ArgoCD

Create the ArgoCD namespace:

```bash
kubectl create namespace argocd
```

Install ArgoCD:

```bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify the ArgoCD pods:

```bash
kubectl get pods -n argocd
```

Wait until the ArgoCD components are running.

### Step 4: Deploy the ArgoCD Application

Apply the ArgoCD Application manifest:

```bash
kubectl apply -f argocd/application.yaml
```

Verify the application:

```bash
kubectl get application nginx-gitops -n argocd
```

### Step 5: Verify Deployment

Check Kubernetes resources:

```bash
kubectl get deployments

kubectl get pods

kubectl get services
```

Expected NGINX deployment:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

### Step 6: Access the NGINX Application

Start port forwarding:

```bash
kubectl port-forward svc/nginx-service 8086:80
```

Open the following URL in your browser:

```text
http://localhost:8086
```

The NGINX welcome page should be displayed.

## Testing Automated Synchronization

The automated synchronization feature can be tested by modifying a Kubernetes manifest.

1. Open `kubernetes/deployment.yaml`.
2. Change a deployment configuration, such as the replica count.
3. Commit and push the changes to GitHub.
4. Open the ArgoCD application.
5. Observe ArgoCD detecting the new Git revision.
6. Verify that Kubernetes automatically applies the updated configuration.

Useful commands:

```bash
git add .

git commit -m "Test ArgoCD automatic synchronization"

git push origin main
```

Verify:

```bash
kubectl get deployment nginx-deployment

kubectl get application nginx-gitops -n argocd
```

## Testing Self-Healing

Self-healing is one of the most important features demonstrated in this project.

The desired configuration specifies three NGINX replicas.

To test self-healing, manually scale the deployment down to one replica.

### Step 1: Manually Change the Replica Count

```bash
kubectl scale deployment nginx-deployment --replicas=1
```

### Step 2: Observe the Deployment

```bash
kubectl get deployment nginx-deployment -w
```

### Step 3: Observe ArgoCD

```bash
kubectl get application nginx-gitops -n argocd
```

ArgoCD detects that the live cluster configuration differs from the desired configuration stored in Git.

Because automated self-healing is enabled, ArgoCD restores the deployment to three replicas.

### Step 4: Verify Recovery

```bash
kubectl get deployment nginx-deployment

kubectl get application nginx-gitops -n argocd
```

Expected result:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

Expected ArgoCD status:

```text
NAME           SYNC STATUS   HEALTH STATUS
nginx-gitops   Synced        Healthy
```

**Result:** Self-healing was successfully tested. ArgoCD automatically restored the desired replica count after a manual modification to the Kubernetes deployment.

## Useful Kubernetes Commands

Check cluster nodes:

```bash
kubectl get nodes
```

Check pods:

```bash
kubectl get pods
```

Check deployments:

```bash
kubectl get deployments
```

Check services:

```bash
kubectl get services
```

Check ArgoCD application:

```bash
kubectl get application nginx-gitops -n argocd
```

Describe ArgoCD application:

```bash
kubectl describe application nginx-gitops -n argocd
```

Check deployment rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

## Screenshots

Screenshots demonstrating the project execution and verification will be maintained in the `screenshots/` directory.

| Screenshot | Description |
|---|---|
| ArgoCD Dashboard | Application showing Synced and Healthy |
| Kubernetes Pods | Three NGINX pods running |
| NGINX Webpage | NGINX welcome page in browser |
| Self-Healing | Deployment automatically restored to three replicas |

<!-- Add screenshots here after capturing them. -->

## Key Learnings

Through this project, I gained practical experience with:

- GitOps principles and workflows.
- Kubernetes deployments and services.
- ArgoCD installation and configuration.
- Connecting ArgoCD to GitHub repositories.
- Automated synchronization of Kubernetes resources.
- Self-healing and desired-state reconciliation.
- Declarative infrastructure management.
- Monitoring application health and synchronization status.

## Future Improvements

- Integrate a CI pipeline to validate Kubernetes manifests.
- Add Helm-based application deployment.
- Configure monitoring using Prometheus and Grafana.
- Add notifications for synchronization and health events.
- Implement separate development and production environments.

## Repository

**GitHub:** https://github.com/RAkSHITH256/argocd-gitops-project

## Author

**RAkSHITH256**

GitHub: https://github.com/RAkSHITH256

---

*This project was developed as a hands-on demonstration of GitOps-based Kubernetes deployment, continuous synchronization, and automated self-healing using ArgoCD.*
