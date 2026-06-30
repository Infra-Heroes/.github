# InfraHeroes UG (haftungsbeschränkt) i.G. 🦸

> *You write the app. We handle the rest.*

**NanoStack** is a German-made cloud platform that runs your containers as isolated
Firecracker MicroVMs — not just namespaces and cgroups. Think Fly.io or Render,
but with hardware-level tenant isolation and no US data leaving the country.

---

## Ship in 60 seconds

```bash
curl -sSL https://infra-heroes.de/install.sh | bash   # install heroctl

heroctl login
heroctl deploy --project my-project
# → https://my-api.my-project.infra-heroes.de  ✓
```

TLS, networking, health checks, secrets — handled. You didn't touch a config file for any of it.

---

## Why not just use containers?

Containers share a kernel. That's the problem.

One compromised dependency, one misconfigured process — and every tenant on that host is a potential victim. We don't think that's a good trade-off, even at small scale.

Every workload on NanoStack runs in its **own Firecracker MicroVM** — a separate kernel, a separate network stack, a separate trust boundary. And because Firecracker was built for exactly this, cold starts are **under 20 ms**. Scale-to-zero actually makes sense.

```
 your image
     │
     ▼  heroctl deploy
     │
     ▼  Nomad schedules the task
     │
     ▼  Compute Agent boots a Firecracker MicroVM
     │       ↳ own kernel
     │       ↳ own VXLAN network namespace
     │       ↳ secrets injected via Vault MMDS
     ▼
  live HTTPS endpoint
```

---

## What you get

| | |
|---|---|
| ⚡ **< 20 ms cold starts** | Scale-to-zero without the latency penalty |
| 🔒 **Isolated kernels** | Hardware-level separation between every tenant |
| 🌐 **BGP EVPN / VXLAN overlay** | Private L2 network per project, across any number of nodes |
| 🔑 **Zero-touch secrets** | Vault injects at boot — never in your image, never in `.env` |
| 📊 **Full observability** | Metrics, logs, traces — Mimir + Loki + Tempo + Grafana, pre-wired |
| 🛍️ **App Store** | One-click Postgres, Redis, and monitoring stacks |
| 🇩🇪 **German infrastructure** | DSGVO-compliant, data stays in Germany |

---

## heroctl

Everything you need, nothing you don't.

### Get started

```bash
heroctl signup                        # create your account
heroctl login                         # authenticate
heroctl orgs                          # list your organisations
```

### Projects

```bash
heroctl projects list                 # list all projects
heroctl projects create my-project    # create a new project
heroctl projects delete my-project    # delete a project
```

### Deploy

```bash
heroctl validate                      # check hero.toml before deploying
heroctl deploy --project my-project   # build & deploy from the current directory
```

### Manage running workloads

```bash
heroctl deployments list     --project my-project
heroctl deployments get      my-api  --project my-project
heroctl deployments start    my-api  --project my-project
heroctl deployments stop     my-api  --project my-project
heroctl deployments restart  my-api  --project my-project
heroctl deployments update   my-api  --cpu 2 --memory 1024 --project my-project
heroctl deployments delete   my-api  --project my-project
```

### Logs

```bash
heroctl logs my-api --project my-project      # recent log snapshot
heroctl logs my-api --project my-project -f   # live stream
```

### Secrets

```bash
heroctl secrets set    DB_PASSWORD --project my-project   # prompted — never in shell history
heroctl secrets list               --project my-project   # keys only, values never shown
heroctl secrets delete DB_PASSWORD --project my-project
```

### Persistent volumes

```bash
heroctl volumes create my-data --size 20 --project my-project   # 20 GB Ceph block volume
heroctl volumes list                     --project my-project
heroctl volumes destroy my-data          --project my-project
```

### Team & access

```bash
heroctl members invite   user@example.com  --org my-org
heroctl members list                       --org my-org
heroctl members set-role <id> admin        --org my-org
heroctl members remove   <id>              --org my-org

heroctl members invitations list           --org my-org
heroctl members invitations revoke <id>    --org my-org
heroctl accept-invite <token>
```

### CI/CD tokens

```bash
heroctl tokens create   --org my-org   # scoped token for your pipeline
heroctl tokens list     --org my-org
heroctl tokens delete <id> --org my-org
```

---

## One config file, zero surprises

```toml
# hero.toml  —  lives next to your Dockerfile
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
"metrics.scrape" = "true"   # auto-discovered by Prometheus

[[volumes]]
name  = "uploads"
mount = "/var/lib/uploads"
```

Sensitive values stay out entirely — `heroctl secrets set KEY` injects them at boot.

---

🔧 **MVP in active development** · closed beta targeting Q2 2026 · [infra-heroes.de](https://infra-heroes.de)
