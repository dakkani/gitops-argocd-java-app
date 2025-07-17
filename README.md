# GitOps Project with Kind and ArgoCD 🚀

This guide will walk you through setting up a local Kubernetes environment using Kind, installing ArgoCD, and deploying an application using GitOps principles.

## Prerequisites 🛠️

Before you begin, ensure you have the following installed:

*   **Docker:** Install Docker Desktop from the [official website](https://www.docker.com/products/docker-desktop/). For Linux, follow the [official installation guide](https://docs.docker.com/engine/install/).

## Installation ⚙️

### 1. Install kubectl

`kubectl` is the command-line tool to interact with your Kubernetes cluster.

```bash
# Download the latest release
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Install kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Verify installation
kubectl version --client
```

### 2. Install Kind

`kind` lets you run Kubernetes on your local machine using Docker containers as nodes.

```bash
# Download the binary
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.29.0/kind-linux-amd64

# Make the binary executable
chmod +x ./kind

# Move the binary to your PATH
sudo mv ./kind /usr/local/bin/kind
```

## Cluster Setup ☸️

Create a Kubernetes cluster with a specific port mapping using the `kind-config.yaml` file in this repository.

```bash
kind create cluster --config kind-config.yaml
```

Verify that your cluster is running:

```bash
kubectl get nodes
```

## ArgoCD Setup 🔄

### 1. Install ArgoCD

Create a namespace for ArgoCD and apply the installation manifests.

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait for all the pods to start. You can check their status with:

```bash
kubectl get pods -n argocd
```

### 2. Access the ArgoCD UI

Forward the ArgoCD server service to your local machine.

```bash
kubectl -n argocd port-forward svc/argocd-server 8080:443
```

Open your browser and navigate to `https://localhost:8080`.

### 3. Log in to ArgoCD

*   **Username:** `admin`
*   **Password:** Get the default admin password by running:

    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

## Deploying the Application 🚀

1.  Click on **"New App"** in the ArgoCD UI.
2.  **Application Name:** Choose any name for your application.
3.  **Project:** `default`
4.  **Sync Policy:** `Automatic`
5.  **Repository URL:** Enter the URL of your GitHub repository where the `deployment.yaml` is located.
6.  **Path:** `.`
7.  **Cluster URL:** `https://kubernetes.default.svc`
8.  **Namespace:** `default`
9.  Click **"Create"**.

## Accessing Your Application 🎉

After a few moments, ArgoCD will automatically sync the application. You can access it at `http://localhost:30080`.

Congratulations! You have successfully deployed an application using GitOps. ✨
