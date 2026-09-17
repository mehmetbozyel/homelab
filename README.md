homelab_v2/
│
├── README.md
│
├── bootstrap/
│   └── argocd/
│       └── root-app.yaml
│
├── clusters/
│   └── homelab/
│       ├── platform.yaml
│       └── workloads.yaml
│
├── platform/
│   ├── gitops/
│   │   └── argocd/
│   │
│   ├── networking/
│   │   ├── traefik/
│   │   └── cloudflare/
│   │
│   ├── observability/
│   │   ├── prometheus/
│   │   ├── grafana/
│   │   └── logging/
│   │
│   ├── storage/
│   │
│   └── security/
│       └── secrets/
│
├── workloads/
│   ├── online-boutique/
│   │   ├── application.yaml
│   │   ├── values.yaml
│   │   └── README.md
│   │
│   └── portfolio/
│       ├── application.yaml
│       ├── values.yaml
│       └── README.md
│
└── docs/
    ├── architecture.md
    ├── bootstrap.md
    └── operations.md