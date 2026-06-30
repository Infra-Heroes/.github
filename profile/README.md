# InfraHeroes

**We build NanoStack — a self-hosted PaaS that runs your containers as isolated Firecracker MicroVMs.**

Push a Docker image. Run `heroctl deploy`. Get a live HTTPS URL. No Kubernetes. No DevOps team required.

---

## What we're building

| | |
|---|---|
| **Hardware-isolated workloads** | Every deployment runs in its own Firecracker MicroVM with a separate kernel — not just cgroups |
| **< 20 ms cold starts** | MicroVM snapshots make scale-to-zero practical for real production workloads |
| **Tenant-level network isolation** | Per-tenant VXLAN overlays via BGP EVPN — no shared L2 broadcast domain between customers |
| **Zero-config secrets** | HashiCorp Vault injects secrets at boot via the Firecracker MMDS — no secrets in env files |
| **Full observability** | Mimir + Loki + Tempo + Grafana, pre-wired, available in one click from the App Store |
| **Made in Germany** | DSGVO-compliant by design, running in German data centres |

---

## Repositories

| Repo | Description |
|------|-------------|
| [heroctl](https://github.com/Infra-Heroes/heroctl) | CLI — deploy, scale, stream logs, manage secrets and volumes |
| [hero-api](https://github.com/Infra-Heroes/hero-api) | Control plane API — projects, billing, auth |
| [hero-compute-agent](https://github.com/Infra-Heroes/hero-compute-agent) | Worker daemon — manages Firecracker MicroVM lifecycles via `/dev/kvm` |
| [hero-driver](https://github.com/Infra-Heroes/hero-driver) | Nomad task driver plugin — bridges scheduler to compute agent |
| [hero-gateway-agent](https://github.com/Infra-Heroes/hero-gateway-agent) | Jump node agent — configures VXLAN bridges and BGP EVPN routes |
| [hero-public](https://github.com/Infra-Heroes/hero-public) | Architecture docs, concepts, roadmap, and pitch materials |
| [hero-app-store](https://github.com/Infra-Heroes/hero-app-store) | One-click managed services (databases, monitoring stack) |

---

## The developer experience

```bash
# Install
curl -sL https://infra-heroes.de/install.sh | bash

# Deploy
heroctl deploy --project my-project

# Stream logs
heroctl logs my-api --project my-project -f

# Inject a secret
heroctl secrets set DB_PASSWORD --project my-project
```

Your `hero.toml` is the entire config surface:

```toml
[app]
name = "my-api"

[deploy]
cpu         = 1
memory_mb   = 512
port        = 3000
health_path = "/health"
```

---

## Status

Currently in active development — targeting closed beta Q2 2026.

Built by [Dominik Ludwig](https://github.com/dominikludwig) and [Sascha Jullmann](https://github.com/SaschaJullmann).

[infra-heroes.de](https://infra-heroes.de)
