Dependency Graph

                         frontend
                            │
          ┌─────────────────┼──────────────────┐
          │                 │                  │
          ▼                 ▼                  ▼
 productcatalog       cartservice        recommendation
                          │                    │
                          ▼                    │
                      redis-cart               │
                                               │
                        productcatalog ◄────────┘

frontend
   │
   ├── currencyservice
   ├── shippingservice
   ├── adservice
   └── checkoutservice
           │
           ├── cartservice
           ├── productcatalogservice
           ├── currencyservice
           ├── shippingservice
           ├── paymentservice
           └── emailservice