# Add A New App

This repository owns application manifests. Cluster-level Argo CD root apps,
projects, and ApplicationSets live in `srudyka/lab01-infra` under:

```text
infrastructure/argocd/clusters/<env>
```

## Add Application Manifests

Use `sample-nginx` as the reference pattern.

1. Create the app base:

```bash
mkdir -p apps/my-app/base
```

Add Kubernetes manifests:

```text
apps/my-app/base/deployment.yaml
apps/my-app/base/service.yaml
apps/my-app/base/kustomization.yaml
```

Example `apps/my-app/base/kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
```

2. Create environment overlays:

```bash
mkdir -p apps/my-app/overlays/dev
mkdir -p apps/my-app/overlays/stage
mkdir -p apps/my-app/overlays/prod
```

Example `apps/my-app/overlays/dev/kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: apps-my-app
resources:
  - ../../base
replicas:
  - name: my-app
    count: 1
labels:
  - pairs:
      environment: dev
    includeSelectors: true
```

Use larger replica counts or stricter settings in `stage` and `prod`.

3. Validate locally:

```bash
kubectl kustomize apps/my-app/overlays/dev
kubectl kustomize apps/my-app/overlays/stage
kubectl kustomize apps/my-app/overlays/prod
```

4. Register the app in cluster config.

In `srudyka/lab01-infra`, edit:

```text
infrastructure/argocd/clusters/dev/applicationsets.yaml
infrastructure/argocd/clusters/stage/applicationsets.yaml
infrastructure/argocd/clusters/prod/applicationsets.yaml
```

Add one element per environment:

```yaml
          - app: my-app
            path: apps/my-app/overlays/dev
            namespace: apps-my-app
```

5. Commit and push both repos as needed:

```bash
git add apps/my-app
git commit -m "Add my-app manifests"
git push
```

Argo CD will discover the new Application after the matching environment root
syncs from `lab01-infra`.

## Production Rules

Keep secrets out of this repository. Use External Secrets, SOPS, or another
approved secret operator.

Keep production changes explicit. Review the `prod` overlay and matching
`lab01-infra` ApplicationSet change before merging.

Avoid committing generated files, Terraform state, or local-only artifacts.
