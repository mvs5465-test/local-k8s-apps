# Release Notes

## [Unreleased]

### Removed
- Outline application, host-mounted dependencies, and related dashboard/health-check references
- Homepage application and its Gatus health check
- Ollama MCP bridge application, external Ollama proxy app, and supporting manifests
- Chat application and its homepage/status references

### Changed
- App manifests are now grouped by ArgoCD sync wave (`apps/wave-0`, `apps/wave-1`, `apps/wave-2`) instead of `apps/system` and `apps/services`
- Cluster-query-router now connects directly to the in-cluster Ollama service
- Grafana now provisions the dotdc community Kubernetes dashboards in a dedicated folder
- Grafana now provisions baseline Kubernetes alert rules and a default notification policy from Git
- Gatus health checks now cover cluster-home, cluster-lite-wiki, and cluster-query-router, and remove the stale Ollama check
- Grafana now provisions official Loki and Mimir dashboards in an Observability Community folder
- Grafana now provisions the official Tempo Writes dashboard in the Observability Community folder
- k8s-monitoring now remote-writes metrics to both Prometheus and Mimir
- k8s-monitoring pod log collection now excludes Loki and Alloy self-logs to avoid recursive ingestion pressure
- Mimir now disables ingest storage explicitly so it can run without Kafka
- Grafana and Prometheus chart pins now track the latest published upstream releases
- k8s-monitoring now exposes OTLP receivers and forwards application traces to Tempo
- github-pr-slack-notifier now deploys to its own namespace (`github-pr-slack-notifier`) with matching ESO secret fan-out
- Grafana canary heartbeat alert is removed in favor of the dedicated CronJob status reporter message
- Cluster health reporter heartbeat now includes styled Slack payloads, trend arrows, sparklines, mood badges, top talkers, and periodic celebration/personality callouts

### Fixed
- Grafana alert contact point now uses native Slack notifier payload formatting to avoid incoming webhook HTTP 400 payload mismatch errors
- k8s-monitoring now writes pod logs to the Loki service on port 3100 so log ingestion succeeds again
- Mimir now uses single-node replication settings that match the local one-replica topology
- Prometheus no longer defines a duplicate self-scrape job after the chart upgrade, which fixes the server crash loop
- Mimir gateway now serves a stub `/metrics` response so chart 6.0.5 no longer reports a false down scrape

### Added
- Grafana Mimir as a dedicated metrics backend in the monitoring namespace
- Grafana Tempo as a dedicated tracing backend in the monitoring namespace
- GitHub PR Slack notifier ArgoCD application (`apps/wave-2/github-pr-slack-notifier-app.yaml`)
- Cluster health reporter ArgoCD application and 30-minute Slack status CronJob (`apps/wave-2/cluster-health-reporter-app.yaml`, `manifests/cluster-health-reporter/cronjob.yaml`)
- External Secrets fan-out manifest for notifier runtime credentials (`manifests/external-secrets/github-pr-slack-notifier-secret.yaml`)
- External Secrets fan-out manifest for Grafana alerting Slack webhook (`manifests/external-secrets/grafana-alerting-secret.yaml`)
- Grafana alerting values file with pod crashloop, pod not-ready, and restart-spike rules (`values/grafana/alerting.yaml`)

## [v1.4.0] - 2026-02-22 - AI Stack & Documentation Improvements

### Added
- **Ollama AI Inference** — LLM inference server with qwen2:0.5b model, persistent storage
- **Open WebUI Chat** — Chat interface connected to Ollama for interactive LLM queries
- **Pod Metrics Dashboard** — New Grafana dashboard with resource usage drilldown by pod
- AI namespace (ai) for Ollama and Chat services

### Changed
- Grafana dashboards refactored into separate value files for better organization
- Renamed `helm/` directory to `values/` for clarity
- Improved README with "Apps Included" section featuring icons and organized tables
- ServiceMonitor moved to dedicated `manifests/servicemonitors/` directory

### Fixed
- Ollama ingress config removed (uses defaults)
- Removed fileserver directory and associated app
- Cluster Overview dashboard panel organization and removed unused network I/O panel

### Removed
- jobs-app.yaml (no longer needed)
- fileserver app and manifests

## [v1.3.0] - 2026-02-22 - ArgoCD Metrics & Observability

### Added
- **Prometheus Operator CRDs** — installs monitoring.coreos.com CRDs for ServiceMonitor support
- **ArgoCD Metrics Scraping** — static Prometheus scrape job for all 4 ArgoCD components
- **ArgoCD ServiceMonitors** — ServiceMonitor resources for ArgoCD metrics discovery
- **ArgoCD Overview Grafana Dashboard** — 9 panels covering sync status, reconcile duration, git requests, redis usage
- Sync waves at app-of-apps level (system: wave 0, services: wave 1) for correct CRD ordering
- nginx-ingress set to sync-wave -1 for highest priority deployment

### Fixed
- ServiceMonitors moved from system to services app to deploy after CRDs exist
- prometheus-operator-crds uses ServerSideApply to handle large CRD annotations
- Grafana out-of-sync/unhealthy panels now show 0 instead of no data
- Removed Log Levels panel from Loki dashboard

## [v1.2.0] - 2026-02-21 - Outline Wiki Stable Release

### Added
- **Outline Wiki** — Stable personal wiki with real-time collaboration support
  - PostgreSQL persistence via hostPath volumes in ~/outline/postgres
  - Redis for real-time collaboration sessions and performance
  - Integrated into Gatus uptime monitoring dashboard
  - Added to Homepage service discovery widget
  - Data persists through Colima VM restarts

### Fixed
- Outline PostgreSQL hostPath permissions via fsGroup (500)
- Outline memory tuning with NODE_OPTIONS for stable startup
- Outline FORCE_HTTPS disabled for Kubernetes health checks
- Outline service networking with headless PostgreSQL and Redis services
- Homepage bookmarks configuration structure per spec
- Grafana dashboard auto-star on load

### Changed
- Outline now documented as stable Services Tier application
- Removed External Secrets Operator (no longer needed after fixes)
- Homepage bookmarks restructured with GitHub repo links
- Updated all service manifests to documentation

## [v1.0.0] - 2026-02-20 - Phase 1 Complete: Custom Dashboards & Monitoring

### Added
- **Cluster Overview Dashboard** — custom 6-panel Grafana dashboard with verified PromQL queries:
  - Node CPU/memory utilization (stat panels with thresholds)
  - Pod CPU/memory usage (timeseries by pod + namespace)
  - Pod restart counts (1h window, bar chart)
  - Node network I/O (RX/TX on same panel, bytes/sec)
  - Replaces broken gnetId:12740 with working, maintainable alternative
- **Loki Logs Dashboard** — custom log browser dashboard:
  - Namespace + Pod dropdowns (populated from Loki labels)
  - Log volume graph (stacked bars by namespace)
  - Log stream viewer with expandable details
  - Log level filter dropdown: error|warn (default), error, warn, info, debug, all
  - Uses correct LogQL operators (`|~` for line filtering)
  - Replaces broken gnetId:13186 (2020, incompatible with Loki 3.x strict matchers)
- Dashboards stored as inline JSON in Helm values — GitOps-managed by ArgoCD, survive pod restarts

### Changed
- Grafana dashboards now custom (stored in `grafana-app.yaml` values)
- Removed imported gnetId dashboards (gnetId:12740, gnetId:13186)

### Fixed
- Pod-level CPU metrics: use `container_cpu_usage_seconds_total{cpu="total"}` (cadvisor no longer exposes per-container CPU)
- Loki dashboard template variables: `query` field must be comma-separated string (not empty) for Grafana to render options
- Loki line filtering: replaced `| regexp` with `|~` (correct LogQL operator for content filtering)
- Loki matcher patterns: use `.+` instead of `.*` to avoid "empty-compatible" matcher rejection in Loki 3.x

### Dashboards Details
All dashboard JSON stored in `apps/wave-1/grafana-app.yaml` under `dashboards.default`:
- **cluster-overview** — custom inline JSON
- **loki-logs** — custom inline JSON

## [v0.1.0] - Initial Release

Initial applications deployment structure with Prometheus, Grafana, Loki, Promtail, Homepage, Gatus.
