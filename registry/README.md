# Lightweight Registry

This directory contains a minimal Docker Registry deployment for a local or lab Kubernetes cluster.

Contents:

- `namespace.yaml`: registry namespace
- `registry.yaml`: `registry:2` Deployment, Service, and PVC
- `kustomization.yaml`: bootstrap entrypoint

Characteristics:

- much lighter than Harbor
- no UI
- no vulnerability scanning
- no built-in RBAC or project management
- persistent image storage on `local-path`
- exposed through `NodePort 30084`

Install:

```bash
kubectl apply -k registry
```

Usage:

- push target example: `NODE_IP:30084/my-image:tag`
- for plain HTTP, Docker or containerd must trust the registry as insecure

Notes:

- this manifest does not enable authentication or TLS
- if you later need users, projects, replication, or scanning, Harbor is the heavier but more complete option
