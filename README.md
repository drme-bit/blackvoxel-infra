kubectl -n blackvoxel create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username="" \
  --docker-password="" \
  --dry-run=client -o yaml \
| kubeseal --format=yaml \
  --namespace blackvoxel \
  --name ghcr-secret \
> apps/common/ghcr-sealedsecret.yaml

Creatig SealedSecret

kubectl -n blackvoxel create secret generic backend-env \
  --from-env-file="ENV"  \
  --dry-run=client -o yaml \
| kubeseal --format=yaml \
  --namespace blackvoxel \
  --name backend-env \
> apps/common/backend-sealedsecret.yaml
