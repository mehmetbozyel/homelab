# Homelab

This repository contains the GitOps and Kubernetes configuration for my homelab.

The main idea is to keep the cluster configuration, platform components, and workloads in Git and manage them with Argo CD.

## Bootstrap

### 1. Install Argo CD

Add the Argo Helm repository:

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```

Create the namespace:

```bash
kubectl create namespace argocd
```

Install Argo CD:

```bash
helm install argocd argo/argo-cd \
  --namespace argocd \
  --version 10.9.1 \
  -f kubernetes/platform/gitops/argocd/values.yaml
```

Get the initial admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

Login to Argo CD using port-forward:

```bash
argocd login argocd-server \
  --port-forward \
  --port-forward-namespace argocd \
  --plaintext
```

---

## 2. Configure Git Repository Access

Generate a dedicated SSH key for Argo CD:

```bash
ssh-keygen -t ed25519 \
  -C "argocd-homelab" \
  -f ~/.ssh/argocd-homelab
```

Print the public key:

```bash
cat ~/.ssh/argocd-homelab.pub
```

Add this key to the GitHub repository as a read-only Deploy Key.

Then add the repository to Argo CD:

```bash
argocd repo add git@github.com:mehmetbozyel/homelab.git \
  --ssh-private-key-path ~/.ssh/argocd-homelab \
  --port-forward \
  --port-forward-namespace argocd \
  --plaintext
```

---

## 2. Create Password for Grafana admin

```bash
kubectl create secret generic grafana-admin \
  -n monitoring \
  --from-literal=admin-user=admin \
  --from-literal=admin-password='StrongPassword'
```

---

## 3. Bootstrap GitOps

The root Argo CD application is applied manually once:

```bash
kubectl apply -f argocd/bootstrap/root-app.yaml
```

After this step, Argo CD manages the platform and workload applications from Git.

The flow is basically:

```text
root-app
├── platform
│   ├── argocd
│   ├── monitoring
│   ├── networking
│   └── ...
│
└── workloads
    ├── online-boutique
    └── portfolio
```

---

## 4. Create Required Secrets

Secrets are currently created manually and are not stored in Git.

### GHCR

This secret is used by workloads that pull private images from GitHub Container Registry.

```bash
kubectl create secret docker-registry ghcr-secret \
  --namespace online-boutique \
  --docker-server=ghcr.io \
  --docker-username=mehmetbozyel \
  --docker-password='<GITHUB_PAT>'
```

### Cloudflare Tunnel

The Cloudflare Tunnel token is also created manually:

```bash
kubectl create secret generic tunnel-token \
  --namespace cloudflare \
  --from-literal=token='<CLOUDFLARE_TUNNEL_TOKEN>'
```

Secret management will probably be moved to a GitOps-friendly solution such as SOPS later.

---

# Repository Structure

```text
homelab/
├── argocd/
│   ├── bootstrap/
│   │   └── root-app.yaml
│   │
│   ├── clusters/
│   │   └── homelab/
│   │       ├── platform.yaml
│   │       └── workloads.yaml
│   │
│   ├── platform/
│   │   ├── argocd.yaml
│   │   ├── monitoring.yaml
│   │   ├── networking.yaml
│   │   └── ...
│   │
│   └── workloads/
│       ├── online-boutique.yaml
│       └── portfolio.yaml
│
└── kubernetes/
    ├── platform/
    │   ├── gitops/
    │   │   └── argocd/
    │   │
    │   ├── networking/
    │   │   └── traefik/
    │   │
    │   ├── observability/
    │   │   └── monitoring/
    │   │
    │   └── storage/
    │
    └── workloads/
        ├── online-boutique/
        │   ├── manifests/
        │   └── chart/
        │
        └── portfolio/
```

## Directory Responsibilities

### `argocd/`

Contains only Argo CD application definitions and bootstrap configuration.

It tells Argo CD **what should be deployed and from where**.

For example:

```text
argocd/workloads/online-boutique.yaml
```

points Argo CD to the actual Kubernetes manifests under:

```text
kubernetes/workloads/online-boutique/
```

### `kubernetes/platform/`

Contains Kubernetes and Helm configuration for cluster-level components.

Examples:

```text
Argo CD
Monitoring
Networking
Storage
Cloudflare Tunnel
```

### `kubernetes/workloads/`

Contains application-specific Kubernetes resources.

Examples:

```text
online-boutique
portfolio
```

Raw Kubernetes manifests are currently kept under `manifests/`.

Helm charts can later be created under `chart/` as the applications become more structured.

---

# GitOps Flow

The general deployment flow is:

```text
GitHub
  ↓
Argo CD
  ↓
Kubernetes
```

For workloads:

```text
Application source repository
        ↓
Container image
        ↓
GHCR
        ↓
Kubernetes manifests in homelab repo
        ↓
Argo CD
        ↓
K3s
```

Platform components such as Argo CD, monitoring, and networking are also managed from this repository.

The goal is to keep manual cluster configuration as small as possible and move the rest of the configuration into Git over time.