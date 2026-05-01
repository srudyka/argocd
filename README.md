# argocd

Application GitOps configuration for Argo CD.

Cluster-level Argo CD bootstrap configuration lives in:

```text
github.com/srudyka/lab01-infra
infrastructure/argocd/clusters/<env>
```

This repository owns application manifests only.

## Layout

```text
apps/<app>/base
apps/<app>/overlays/dev
apps/<app>/overlays/stage
apps/<app>/overlays/prod
```

Each application keeps shared Kubernetes manifests in `base` and environment
differences in `overlays/<env>`.

## Add Applications

See `BOOTSTRAP.md`.

## Production Notes

Keep secrets out of this repo. Use External Secrets, SOPS, or another approved
secret operator.

Require pull requests and reviews before changing production overlays.
