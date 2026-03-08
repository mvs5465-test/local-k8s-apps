# Local K8s Applications

ArgoCD `Application` definitions grouped by sync wave. Pair with [`local-k8s-argocd`](https://github.com/mvs5465-test/local-k8s-argocd), which reads these manifests via an `ApplicationSet` and generates the live applications.

## Storage Configuration

Data is stored under `~/clusterstorage/` with the following structure:
```
~/clusterstorage/
└── files/              ← Jellyfin media files (nginx file server)
```

See `local-k8s-argocd` README for Colima mount configuration.

## Apps Included

### Wave 0
| | | |
|---|---|---|
| 🌐 **Nginx Ingress** | External traffic routing | [nginx-ingress-app.yaml](apps/wave-0/nginx-ingress-app.yaml) |

### Wave 1
| | | |
|---|---|---|
| 🔐 **External Secrets** | Secret operator and CRDs | [external-secrets-app.yaml](apps/wave-1/external-secrets-app.yaml) |
| 📦 **Mimir** | Long-term Prometheus-compatible metrics backend | [mimir-app.yaml](apps/wave-1/mimir-app.yaml) |
| 🧭 **Tempo** | Distributed tracing backend | [tempo-app.yaml](apps/wave-1/tempo-app.yaml) |
| 📊 **Prometheus** | Metrics collection and storage | [prometheus-app.yaml](apps/wave-1/prometheus-app.yaml) |
| 📈 **Grafana** | Dashboards & visualization | [grafana-app.yaml](apps/wave-1/grafana-app.yaml) |
| 📝 **Loki** | Log aggregation backend | [loki-app.yaml](apps/wave-1/loki-app.yaml) |

### Wave 2
| | | |
|---|---|---|
| 🔐 **External Secrets Config** | Cluster secret definitions and bindings | [external-secrets-config-app.yaml](apps/wave-2/external-secrets-config-app.yaml) |
| 📡 **K8s Monitoring** | Alloy collectors and telemetry routing | [k8s-monitoring-app.yaml](apps/wave-2/k8s-monitoring-app.yaml) |
| 🌌 **Cluster Home** | Custom dark-themed service dashboard | [cluster-home-app.yaml](apps/wave-2/cluster-home-app.yaml) |
| 📚 **Cluster Lite Wiki** | Seeded local cluster documentation | [cluster-lite-wiki-app.yaml](apps/wave-2/cluster-lite-wiki-app.yaml) |
| 🔎 **Cluster Query Router** | In-cluster query entrypoint | [cluster-query-router-app.yaml](apps/wave-2/cluster-query-router-app.yaml) |
| 🩺 **Cluster Health Reporter** | Slack heartbeat with interpreted cluster health summary every 30 minutes | [cluster-health-reporter-app.yaml](apps/wave-2/cluster-health-reporter-app.yaml) |
| 📊 **Gatus** | Uptime monitoring & status page | [gatus-app.yaml](apps/wave-2/gatus-app.yaml) |
| 🎬 **Jellyfin** | Media server | [jellyfin-app.yaml](apps/wave-2/jellyfin-app.yaml) |
| 🪵 **Loki MCP** | Loki MCP bridge service | [loki-mcp-app.yaml](apps/wave-2/loki-mcp-app.yaml) |
| 📈 **Prometheus MCP** | Prometheus MCP bridge service | [prometheus-mcp-app.yaml](apps/wave-2/prometheus-mcp-app.yaml) |

## Adding Apps

1. Create `<app-name>-app.yaml` in the appropriate `apps/wave-*/` directory
2. Reference your Helm chart or git repo
3. Push to main and let the bootstrap `ApplicationSet` regenerate the live `Application`

## Development

Create feature branches to test changes. ArgoCD watches main, so changes sync automatically after merge.

See `CLAUDE.md` for development guidelines.
