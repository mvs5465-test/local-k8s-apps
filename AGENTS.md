# Local K8s Applications

This repo contains ArgoCD `Application` definitions. Pair it with `local-k8s-argocd`, which owns bootstrap and shared ArgoCD policy.

## Repo Shape
- Root fan-out applications:
  - `apps/system-app.yaml`
  - `apps/services-app.yaml`
- Child applications live in:
  - `apps/system/`
  - `apps/services/`
- Shared values files live under `values/`

## Working Rules
- Add new apps as `<name>-app.yaml` in `apps/system/` or `apps/services/`.
- Keep this repo focused on ArgoCD application manifests and shared values, not app source code.
- Prefer minimal Helm overrides here; if a chart can own a sensible default, keep the override out of this repo.
- If an app pulls its chart from a git repo, add that repo URL to `local-k8s-argocd/manifests/config/appproject.yaml` `sourceRepos`.
- Do not manually apply child `Application` manifests; let the root apps reconcile them.

## Cross-Repo Coordination
- `local-k8s-argocd/manifests/argocd/app-of-apps-app.yaml` points ArgoCD at this repo.
- `local-k8s-argocd/manifests/config/appproject.yaml` controls whether ArgoCD is allowed to sync each app source repo.
- If you change an app's expected chart path, repo URL, or namespace, verify the matching upstream repo and AppProject policy.
- If you add, remove, or materially change user-facing services, routes, or cluster workflows, update `cluster-lite-wiki/seed/pages/` in the same PR series so first-boot docs stay current.

## Validation
- Check manifest formatting carefully; a bad `Application` spec can break reconciliation.
- When practical, use `kubectl diff -f <file>` or inspect the rendered resource in ArgoCD after merge.
- For Helm values edits, sanity-check that indentation and inline JSON/YAML are still valid.

## Git
- Use Conventional Commits.
- Keep PRs small and scoped to the specific app or deployment concern being changed.
