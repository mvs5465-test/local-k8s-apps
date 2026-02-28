# Local K8s Applications

This repo contains ArgoCD `Application` definitions. Pair it with `local-k8s-argocd` for infrastructure.

## Key Points
- Watched by the root apps in `local-k8s-argocd`.
- Top-level aggregators are `apps/system-app.yaml` and `apps/services-app.yaml`.
- Child applications live in `apps/system/` and `apps/services/`.
- Use Conventional Commits.

## Common Tasks
1. Add new applications as `<name>-app.yaml` in `apps/system/` or `apps/services/`.
2. Point each manifest at the application repo.
3. If the app sources its Helm chart from a git repo rather than a public Helm repo, whitelist that repo URL in `local-k8s-argocd/manifests/config/appproject.yaml` under `sourceRepos`.
4. Push to `main` for ArgoCD auto-discovery.

Example: `loki-mcp-server` pulls from `https://github.com/mvs5465/loki-mcp-server`, so that repo must be listed in `sourceRepos`.

## Testing Changes
1. Create a feature branch.
2. Modify the application manifests.
3. Test locally or in a dev environment.
4. Merge when ready.

## Integration
- `local-k8s-argocd/manifests/argocd/appproject-app.yaml` applies shared project policy.
- `local-k8s-argocd/manifests/argocd/app-of-apps-app.yaml` points ArgoCD at this repo.
- `apps/system-app.yaml` and `apps/services-app.yaml` fan out to the child apps.
- Do not manually apply child applications; let the root apps reconcile them.
