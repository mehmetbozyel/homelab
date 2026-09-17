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