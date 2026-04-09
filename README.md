# Horse Racing k3s Deployment Notes

## 1) Build and push images to Docker Hub

Replace `<dockerhub_id>` and `<tag>` values:

```bash
docker build -t <dockerhub_id>/horse-racing-backend:<tag> ./backend
docker build -t <dockerhub_id>/horse-racing-frontend:<tag> ./frontend

docker login
docker push <dockerhub_id>/horse-racing-backend:<tag>
docker push <dockerhub_id>/horse-racing-frontend:<tag>
```

## 2) Update image names in manifests

Edit these files and replace image names and tag:

- `k8s/backend-deployment.yaml`
- `k8s/frontend-deployment.yaml`

## 3) Create namespace and Docker Hub pull secret

```bash
kubectl apply -f k8s/namespace.yaml

kubectl -n horse-racing create secret docker-registry dockerhub-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<dockerhub_id> \
  --docker-password=<dockerhub_token_or_password> \
  --docker-email=<email>
```

## 4) Create backend runtime secret

Create from example:

```bash
cp k8s/backend-secret.example.yaml k8s/backend-secret.yaml
```

Then fill real values and apply:

```bash
kubectl apply -f k8s/backend-secret.yaml
```

## 5) Deploy workloads

```bash
kubectl apply -k k8s
kubectl -n horse-racing get pods,svc,ingress
```

## 6) Optional: local validation

```bash
kubectl -n horse-racing port-forward svc/frontend 3000:3000
kubectl -n horse-racing port-forward svc/backend 3001:3001
```
