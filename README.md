# blackvoxel-infra (GitOps)

## Ingress (local testing)

### 1) Install ingress-nginx controller (via ArgoCD)

This repo contains an ArgoCD `Application` which installs the `ingress-nginx` controller Helm chart into the cluster in namespace `ingress-nginx`.

After ArgoCD sync, you should see the controller running:

- `kubectl get ns ingress-nginx`
- `kubectl -n ingress-nginx get pods`
- `kubectl -n ingress-nginx get svc`

### 2) Port-forward ingress-nginx

Forward the controller service port `80` to your machine:

- `kubectl -n ingress-nginx port-forward svc/ingress-nginx-controller 8081:80`

### 3) Access your Ingress locally

If your Ingress uses a host like `blackvoxel.studio`, you need to send that `Host` header.

Option A (recommended): add to `/etc/hosts`

- `127.0.0.1 blackvoxel.studio`

Then open:

- `http://blackvoxel.studio:8081/`
- `http://blackvoxel.studio:8081/api/...`

Option B: use curl with a Host header:

- `curl -H 'Host: blackvoxel.studio' http://127.0.0.1:8081/`
- `curl -H 'Host: blackvoxel.studio' http://127.0.0.1:8081/api/health`

## SealedSecrets (how to generate)

### Create GHCR pull secret as SealedSecret

kubectl -n blackvoxel create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username="" \
  --docker-password="" \
  --dry-run=client -o yaml \
| kubeseal --format=yaml \
  --namespace blackvoxel \
  --name ghcr-secret \
> apps/common/ghcr-sealedsecret.yaml

### Create backend env secret as SealedSecret

kubectl -n blackvoxel create secret generic backend-env \
  --from-env-file="ENV"  \
  --dry-run=client -o yaml \
| kubeseal --format=yaml \
  --namespace blackvoxel \
  --name backend-env \
> apps/common/backend-sealedsecret.yaml
