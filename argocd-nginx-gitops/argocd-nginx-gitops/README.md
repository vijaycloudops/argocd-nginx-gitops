# Simple GitOps Deployment with Argo CD

![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestration-326CE5?logo=kubernetes)
![Argo CD](https://img.shields.io/badge/Argo%20CD-GitOps-EF7B4D?logo=argo)
![Nginx](https://img.shields.io/badge/Nginx-Web%20Server-009639?logo=nginx)

## Project Overview

This project demonstrates a simple GitOps workflow using Argo CD and Kubernetes. The repository contains Kubernetes YAML manifests for an Nginx application. Argo CD monitors the Git repository and keeps the Kubernetes cluster synchronized with the manifests stored in Git.

When you update the Kubernetes manifests and push the changes to GitHub, Argo CD detects the change and can synchronize the updated configuration to the cluster.

## GitOps Workflow

```text
Developer changes Kubernetes YAML
              |
              v
        Push to GitHub
              |
              v
      Argo CD monitors Git
              |
              v
      Compares Git and cluster
              |
              v
      Syncs desired configuration
              |
              v
      Nginx runs on Kubernetes
```

## Technologies

- Kubernetes
- Argo CD
- Git and GitHub
- YAML
- Nginx
- kubectl

## Repository Structure

```text
argocd-nginx-gitops/
├── README.md
├── k8s/
│   ├── namespace.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── argocd/
    └── application.yaml
```

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access that cluster
- Argo CD installed in the cluster
- A GitHub repository containing these files

## Setup

### 1. Push this project to GitHub

Create a repository, for example `argocd-nginx-gitops`, and upload the project files.

### 2. Update the Argo CD Application

Open `argocd/application.yaml` and replace:

`https://github.com/YOUR_GITHUB_USERNAME/argocd-nginx-gitops.git`

with your actual GitHub repository URL.

### 3. Create the Argo CD Application

Run:

```bash
kubectl apply -f argocd/application.yaml
```

### 4. Check the application

```bash
kubectl get applications -n argocd
kubectl get pods -n nginx-demo
kubectl get services -n nginx-demo
```

Open the Argo CD UI and find the application named `nginx-gitops`.

The Application is configured with automated sync, pruning, and self-healing. Argo CD will apply changes from Git and remove resources that are deleted from the tracked manifests. Use this setting only when you understand that Git is the source of truth for the managed resources.

## Test GitOps

1. Open `k8s/deployment.yaml`.
2. Change the replica count from `2` to `3`.
3. Commit and push the change to the `main` branch.
4. Watch the Argo CD application detect the Git change and synchronize the cluster.
5. Verify the Pods:

```bash
kubectl get pods -n nginx-demo
```

You should see three Nginx Pods after the rollout completes.

## Access Nginx

For a local cluster, use port forwarding:

```bash
kubectl port-forward service/nginx-service 8080:80 -n nginx-demo
```

Open `http://localhost:8080`.

## Interview Explanation

"I created a simple GitOps deployment using Argo CD and Kubernetes. I stored the Nginx Kubernetes manifests in a GitHub repository and configured an Argo CD Application to monitor that repository. When I change the desired configuration in Git and push it, Argo CD detects the difference and synchronizes the Kubernetes cluster. This demonstrates GitOps, declarative deployment, automated synchronization, and drift correction."

Only say that automated synchronization and self-healing worked after you have installed Argo CD and tested the workflow yourself.

## Cleanup

Delete the Argo CD Application:

```bash
kubectl delete -f argocd/application.yaml
```

If you want to remove Argo CD itself, follow the installation method's cleanup instructions. Do not delete the `argocd` namespace unless you intend to uninstall Argo CD.

## Author

**Vijay Kumar Kunja**

GitHub: [vijaycloudops](https://github.com/vijaycloudops)
