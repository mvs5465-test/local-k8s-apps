# Local K8s Applications

This repo contains ArgoCD `Application` definitions. Pair it with `local-k8s-argocd`, which owns bootstrap and shared ArgoCD policy.

## Repo Shape
- Child applications live in:
  - `apps/bootstrap-wave-0/`
  - `apps/bootstrap-wave-1/`
  - `apps/cluster-wave-1/`
  - `apps/cluster-wave-2/`
- Shared values files live under `values/`

## Working Rules
- Put only true first-boot prerequisites in `apps/bootstrap-wave-0/` or `apps/bootstrap-wave-1/`.
- Put normal steady-state apps in `apps/cluster-wave-1/` or `apps/cluster-wave-2/`.
- The `ApplicationSet` assigns `argocd.argoproj.io/sync-wave` based on the folder, so app manifests should not set that annotation themselves.
- Keep this repo focused on ArgoCD application manifests and shared values, not app source code.
- Prefer minimal Helm overrides here; if a chart can own a sensible default, keep the override out of this repo.
- If an app pulls its chart from a git repo, add that repo URL to `local-k8s-argocd/manifests/config/appproject.yaml` `sourceRepos`.
- Do not manually apply child `Application` manifests; `local-k8s-argocd` reads them as `ApplicationSet` input and generates the live `Application` objects.

## Cross-Repo Coordination
- `local-k8s-argocd/manifests/applicationsets/cluster-apps.yaml` defines both the bootstrap and steady-state `ApplicationSet` resources that read these manifests and generate the live `Application` resources.
- `local-k8s-argocd/manifests/config/appproject.yaml` controls whether ArgoCD is allowed to sync each app source repo.
- If you change an app's expected chart path, repo URL, or namespace, verify the matching upstream repo and AppProject policy.
- If you add, remove, or materially change user-facing services, routes, or cluster workflows, update `cluster-lite-wiki/seed/pages/` in the same PR series so first-boot docs stay current.

## Validation
- Check manifest formatting carefully; a bad `Application` spec can break reconciliation.
- When practical, use `kubectl diff -f <file>` or inspect the rendered resource in ArgoCD after merge.
- For Helm values edits, sanity-check that indentation and inline JSON/YAML are still valid.

## Git
- Use Conventional Commits.
- PR titles must use the same Conventional Commit format as commits: `<type>(<scope>): <description>`.
- Before handing off a PR, verify the latest commit message and the PR title both match that format.
- Keep PRs small and scoped to the specific app or deployment concern being changed.
