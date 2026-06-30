# InfraHeroes UG (haftungsbeschränkt) i.G. 🦸

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

## heroctl — the full CLI

### Account & projects

```bash
heroctl signup                                     # create an account
heroctl login                                      # authenticate
heroctl orgs                                       # list your organisations

heroctl projects list                              # list projects
heroctl projects create my-project                 # create a project
heroctl projects delete my-project                 # delete a project
```

### Deploy & manage workloads

```bash
heroctl validate                                   # validate hero.toml before deploying
heroctl deploy --project my-project                # build & deploy from current directory

heroctl deployments list --project my-project      # list all deployments
heroctl deployments get my-api --project my-project # inspect a deployment
heroctl deployments start   my-api --project …     # start a stopped deployment
heroctl deployments stop    my-api --project …     # stop without deleting
heroctl deployments restart my-api --project …     # rolling restart
heroctl deployments update  my-api --cpu 2 --memory 1024 --project … # scale vertically
heroctl deployments delete  my-api --project …     # remove deployment
```

### Logs

```bash
heroctl logs my-api --project my-project           # snapshot of recent logs
heroctl logs my-api --project my-project -f        # stream live (follow mode)
```

### Secrets

```bash
heroctl secrets set    DB_PASSWORD --project …     # set a secret (read from stdin)
heroctl secrets list   --project my-project        # list secret keys (values hidden)
heroctl secrets delete DB_PASSWORD --project …     # delete a secret
```

### Persistent volumes

```bash
heroctl volumes create my-data --size 20 --project …  # create a 20 GB volume
heroctl volumes list   --project my-project             # list volumes
heroctl volumes destroy my-data --project …            # permanently destroy a volume
```

### Team management

```bash
heroctl members invite   user@example.com --org my-org  # invite a team member
heroctl members list     --org my-org                    # list members
heroctl members set-role <id> admin --org my-org         # promote to admin
heroctl members remove   <id> --org my-org               # remove a member

heroctl members invitations list   --org my-org          # pending invitations
heroctl members invitations revoke <id> --org my-org     # cancel an invite
heroctl accept-invite <token>                            # accept an invitation
```

### CI/CD tokens

```bash
heroctl tokens create --org my-org   # create a scoped API token for CI runners
heroctl tokens list   --org my-org   # list active tokens
heroctl tokens delete <id> --org my-org
```

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
