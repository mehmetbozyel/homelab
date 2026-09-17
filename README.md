kubectl create namespace argocd

kubectl apply -n argocd --server-side --force-conflicts -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d\necho

ssh-keygen -t ed25519 -C "argocd-homelab" -f ~/.ssh/argocd-homelab
cat ~/.ssh/argocd-homelab.pub
argocd repo add git@github.com:mehmetbozyel/homelab.git --ssh-private-key-path ~/.ssh/argocd-homelab

kubectl apply -f argocd/bootstrap/root-app.yaml

kubectl create secret docker-registry ghcr-secret \
  --namespace online-boutique \
  --docker-server=ghcr.io \
  --docker-username=mehmetbozyel \
  --docker-password='<GITHUB_PAT>'


homelab_v2/
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
    │   ├── networking/
    │   │   └── traefik/
    │   ├── observability/
    │   │   └── monitoring/
    │   └── storage/
    │
    └── workloads/
        ├── online-boutique/
        │   ├── manifests/
        │   └── chart/
        │
        └── portfolio/