# InfraHeroes 🦸

> **Container in. MicroVM out. HTTPS up.**
> No Kubernetes. No DevOps team. No vendor lock-in.

We're building **NanoStack** — a German-made PaaS that boots your workloads as isolated
[Firecracker](https://firecracker-microvm.github.io/) MicroVMs instead of shared-kernel containers.
The simplicity of Fly.io or Render, with hardware-level tenant isolation, running on infrastructure you control.

---

## From zero to deployed in 60 seconds

```bash
# 1. Install the CLI
curl -sSL https://infra-heroes.de/install.sh | bash

# 2. Log in & deploy
heroctl login
heroctl deploy --project my-project
# → https://my-api.my-project.infra-heroes.de ✓
```

That's it. TLS, networking, secrets injection, health checks — all handled.

---

## Why MicroVMs?

Containers share a kernel. One exploit, one misconfigured process, one rogue dependency can affect every tenant on the host. Firecracker MicroVMs give every workload its own kernel, its own network stack, and its own trust boundary — with cold starts under **20 ms**.

```
Developer pushes image
        │
        ▼
  heroctl deploy
        │
        ▼
 Nomad schedules task
        │
        ▼
 Compute Agent boots Firecracker MicroVM   ← isolated kernel
        │                                  ← isolated VXLAN namespace
        ▼                                  ← secrets via Vault MMDS
 App is live at your subdomain
```

---

## Platform features

| Feature | What it means for you |
|---------|----------------------|
| ⚡ **< 20 ms cold starts** | Scale-to-zero is actually usable in production |
| 🔒 **Hardware-isolated VMs** | Separate kernel per tenant — not just cgroups |
| 🌐 **EVPN/VXLAN networking** | Full L2 tenant isolation across any number of worker nodes |
| 🔑 **Vault-backed secrets** | Injected at boot via MMDS, never stored in env files or images |
| 📊 **Built-in observability** | Mimir + Loki + Tempo + Grafana, pre-wired for every deployment |
| 🛍️ **App Store** | One-click Postgres, Redis, monitoring stacks |
| 🇩🇪 **Made in Germany** | DSGVO-compliant, data stays in German data centres |

---

## The full config is one file

```toml
# hero.toml
[app]
name           = "my-api"
custom_domains = ["api.example.com"]

[deploy]
cpu           = 2
memory_mb     = 1024
port          = 3000
health_path   = "/health"
min_replicas  = 2
max_replicas  = 5
scale_to_zero = false

[env]
NODE_ENV = "production"

[labels]
"metrics.scrape" = "true"

[[volumes]]
name  = "uploads"
mount = "/var/lib/uploads"
```

Secrets stay out of the file — set them with `heroctl secrets set`.

---

## Status

🔧 **MVP in active development** — closed beta targeting Q2 2026

Built by [Dominik Ludwig](https://github.com/dominikludwig1995) and [Sascha Jullmann](https://github.com/SaschaJullmann)
in Frankfurt, Germany.

**→ [infra-heroes.de](https://infra-heroes.de)**
