# 🚀 GKE Production Cluster — GCP + Terraform + Argo CD

> **Production-grade Google Kubernetes Engine (GKE) cluster provisioned with Terraform, deployed via GitOps (Argo CD), and monitored with Prometheus + Grafana.**  
> Built to mirror real-world multi-service platform infrastructure at scale.

---

## 📐 Architecture Overview

```
                        ┌─────────────────────────────────────────────┐
                        │              Google Cloud Platform            │
                        │                                               │
                        │   ┌──────────────┐    ┌──────────────────┐  │
  Developer ──push──►  │   │  GitHub Repo  │───►│   Argo CD (GitOps│  │
                        │   └──────────────┘    │   Sync Engine)   │  │
                        │                        └────────┬─────────┘  │
                        │                                 │             │
                        │              ┌──────────────────▼──────────┐ │
                        │              │         GKE Cluster          │ │
                        │              │  ┌─────────┐  ┌──────────┐  │ │
  User ──HTTPS──►       │  LB/Ingress──►  │ Node Pool│  │Node Pool │  │ │
                        │  (NGINX)     │  │ (n1-std) │  │ (n1-std) │  │ │
                        │              │  └─────────┘  └──────────┘  │ │
                        │              │                              │ │
                        │              │  Namespaces:                 │ │
                        │              │  • production                │ │
                        │              │  • monitoring                │ │
                        │              │  • argocd                    │ │
                        │              └──────────────────────────────┘ │
                        │                                               │
                        │  ┌────────────┐  ┌──────────┐  ┌─────────┐  │
                        │  │ Cloud SQL  │  │ Cloud    │  │ GCS     │  │
                        │  │ (Postgres) │  │ Monitoring│  │ Bucket  │  │
                        │  └────────────┘  └──────────┘  └─────────┘  │
                        └─────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Cloud Provider | Google Cloud Platform (GCP) |
| Kubernetes | GKE (Google Kubernetes Engine) |
| Infrastructure as Code | Terraform + GCS remote state |
| GitOps / CD | Argo CD |
| CI Pipeline | GitHub Actions |
| Ingress | NGINX Ingress Controller |
| Monitoring | Prometheus + Grafana + Cloud Monitoring |
| Service Mesh (planned) | Istio |
| Secret Management | GCP Secret Manager |

---

## 📁 Repository Structure

```
gke-production-cluster/
├── terraform/                  # GKE cluster provisioning
│   ├── main.tf                 # GKE cluster + node pools
│   ├── variables.tf
│   ├── outputs.tf
│   ├── backend.tf              # GCS remote state
│   └── vpc.tf                  # VPC + subnets
├── k8s/
│   ├── base/                   # Core app manifests
│   │   ├── namespace.yaml
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── hpa.yaml            # Horizontal Pod Autoscaler
│   ├── ingress/
│   │   └── nginx-ingress.yaml
│   └── monitoring/
│       ├── prometheus.yaml
│       └── grafana.yaml
├── argocd/
│   └── application.yaml        # Argo CD app definition
├── .github/
│   └── workflows/
│       └── ci.yaml             # GitHub Actions CI pipeline
└── README.md
```

---

## ⚙️ Infrastructure Details

### GKE Cluster Configuration

| Parameter | Value |
|---|---|
| Cluster type | Regional (multi-zone) |
| Region | `us-central1` |
| Node machine type | `n1-standard-2` |
| Node pool min/max | 2 / 6 (autoscaling) |
| Kubernetes version | `1.28` (stable channel) |
| Networking | VPC-native (alias IPs) |
| Workload Identity | Enabled |
| Binary Authorization | Enabled |

### Networking

- **VPC**: Custom VPC with dedicated subnets for GKE pods and services
- **NGINX Ingress**: Routes external traffic to internal services
- **Network Policies**: Enforced between namespaces

---

## 🚀 Deployment Flow

```
1. Developer pushes code → GitHub
2. GitHub Actions runs CI (lint, test, docker build, push to GCR)
3. Argo CD detects manifest changes → syncs to GKE cluster
4. Rolling update deployed with zero downtime
5. Prometheus scrapes metrics → Grafana dashboards update
6. GCP Cloud Monitoring alerts on SLO breaches
```

---

## 📊 Observability Stack

- **Prometheus**: Scrapes pod metrics via ServiceMonitor CRDs
- **Grafana**: Pre-built dashboards for cluster health, pod CPU/memory, HPA scaling events
- **GCP Cloud Monitoring**: Uptime checks + alerting policies
- **Loki** *(planned)*: Centralized log aggregation

---

## 🔐 Security

- Workload Identity for pod-level GCP IAM (no service account key files)
- Least-privilege IAM roles per namespace
- Secrets stored in GCP Secret Manager (not Kubernetes secrets)
- Network Policies enforced between namespaces
- NGINX Ingress with TLS termination (cert-manager)

---

## 📈 Scalability

- **HPA**: Scales pods based on CPU/memory thresholds
- **Cluster Autoscaler**: Scales node pool from 2 to 6 nodes based on demand
- **Multi-zone node pool**: Ensures HA across GCP availability zones

---

## 🗺️ Roadmap

- [x] GKE cluster with Terraform
- [x] Argo CD GitOps deployment
- [x] NGINX Ingress + TLS
- [x] Prometheus + Grafana monitoring
- [ ] Istio service mesh integration
- [ ] Loki log aggregation
- [ ] Multi-region failover

---

## 👤 Author

**Mohamed Ebrahim Draz** — DevOps Engineer  
📧 drazmohamed542@gmail.com | [LinkedIn](#) | [GitHub](#)
