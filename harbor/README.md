# Harbor Registry

This directory keeps the bootstrap YAML and Helm values for a local Harbor registry deployment.

Contents:

- `namespace.yaml`: Harbor namespace
- `harbor-admin-secret.yaml`: initial admin password secret
- `kustomization.yaml`: bootstrap entrypoint for namespace and secret
- `values.yaml`: Harbor Helm chart overrides tuned for this cluster

Assumptions:

- local or lab Kubernetes cluster
- `local-path` StorageClass is available
- Harbor is exposed with `NodePort`
- internal PostgreSQL and Redis from the Harbor chart are used

Bootstrap the namespace and secret first:

```bash
# Edit harbor/harbor-admin-secret.yaml first.
kubectl apply -k harbor
```

Install Harbor with Helm:

```bash
helm repo add harbor https://helm.goharbor.io
helm repo update
helm upgrade --install harbor harbor/harbor \
  --namespace harbor \
  -f harbor/values.yaml
```

After installation:

- update `externalURL` in `values.yaml` to a real node IP or DNS name before installing
- `existingSecretAdminPassword` is applied only for the initial Harbor bootstrap; later password changes should be done in Harbor itself
- if you use an older Harbor chart without `existingSecretAdminPassword`, set `harborAdminPassword` directly in `values.yaml`
- if you keep HTTP exposure, configure Docker or containerd to trust the registry as insecure for `harbor.local:30082`
- if you want TLS instead, switch `expose.tls.enabled` to `true` and provide a certificate source supported by the Harbor chart
