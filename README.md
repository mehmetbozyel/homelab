helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

kubectl create namespace argocd

helm install argocd argo/argo-cd \
  --namespace argocd \
  --version 10.9.1 \
  -f kubernetes/platform/gitops/argocd/values.yaml

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

argocd login argocd-server \
  --port-forward \
  --port-forward-namespace argocd \
  --plaintext

ssh-keygen -t ed25519 -C "argocd-homelab" -f ~/.ssh/argocd-homelab
cat ~/.ssh/argocd-homelab.pub

argocd repo add git@github.com:mehmetbozyel/homelab.git --ssh-private-key-path ~/.ssh/argocd-homelab \
--port-forward \
--port-forward-namespace argocd \
--plaintext

kubectl apply -f argocd/bootstrap/root-app.yaml

kubectl create secret docker-registry ghcr-secret \
  --namespace online-boutique \
  --docker-server=ghcr.io \
  --docker-username=mehmetbozyel \
  --docker-password='<GITHUB_PAT>'


kubectl create secret generic tunnel-token \
  -n cloudflare \
  --from-literal=token='<CLOUDFLARE_TUNNEL_TOKEN>'




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