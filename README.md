# Pacman Konflux GitOps

Deployment and Konflux tenant configuration for [pacman-konflux](https://github.com/marrober/pacman-konflux).

Application source and build pipelines live in the app repo. This repo holds what Argo CD syncs and image-promotion commits.

## Layout

| Path | Purpose |
| --- | --- |
| `components/pacman/base/` | Deployment, Service, Route |
| `components/pacman/overlays/dev/` | Dev namespace + Kustomize `images` (tag/digest bumps) |
| `argocd/` | Argo CD Application manifests |
| `konflux/` | Konflux Application / Component / ImageRepository (tenant bootstrap) |

## Deploy with Argo CD

```bash
kubectl apply -f argocd/pacman-dev.yaml
```

Argo CD syncs `components/pacman/overlays/dev` into `pacman-konflux-dev`.

## Image updates

After a successful Konflux build, update the image in the overlay (manually or via release automation):

```yaml
# components/pacman/overlays/dev/kustomization.yaml
images:
- name: quay.io/redhat-user-workloads/pacman-konflux/pacman/pacman
  newTag: <git-sha-or-tag>
  # or: digest: sha256:...
```

Commit that change here — not in the app repo — so build history stays separate from deploy history.

## Konflux tenant bootstrap

Apply once into the Konflux tenant namespace (`pacman-konflux`):

```bash
kubectl apply -f konflux/application.yaml
kubectl apply -f konflux/component.yaml
```
