# CourseCompass Kubernetes Infrastructure

Kubernetes manifests for deploying the CourseCompass application.

## Architecture

**Frontend**: React app, 2 replicas, exposed via LoadBalancer (port 80)

**Backend**: API server, 2 replicas, ClusterIP (port 8080)

**PostgreSQL**: StatefulSet, 1 replica, persistent storage (10Gi)

Frontend → Backend → PostgreSQL

## Prerequisites

- Kubernetes v1.19+
- `kubectl` configured
- Container images:
  - `ghcr.io/CourseCompass/CourseCompass-frontend:latest`
  - `ghcr.io/CourseCompass/CourseCompass-backend:latest`

## Setup

### Configure Database Secret

Edit `postgres/secret.yaml` or create it via CLI:

```bash
kubectl create secret generic postgres-secret \
  --from-literal=POSTGRES_USER=your_user \
  --from-literal=POSTGRES_PASSWORD=your_password \
  --from-literal=POSTGRES_DB=coursecompass
```

### Deploy

```bash
# PostgreSQL
kubectl apply -f postgres/

# Backend
kubectl apply -f backend/

# Frontend
kubectl apply -f frontend/
```

Verify:

```bash
kubectl get pods
kubectl get svc
```

## Key Notes

- PostgreSQL uses a StatefulSet with auto-created PVC
- Data persists across pod restarts
- Service discovery:
  - Backend → `postgres-service:5432`
  - Frontend → `backend-service:8080`

## Common Commands

### Logs

```bash
kubectl logs -l app=frontend
kubectl logs -l app=backend
kubectl logs -l app=postgres
```

### Update images

```bash
kubectl rollout restart deployment/backend-deployment
kubectl rollout restart deployment/frontend-deployment
```

### Scale

```bash
kubectl scale deployment backend-deployment --replicas=3
kubectl scale deployment frontend-deployment --replicas=3
```

## Cleanup

```bash
kubectl delete -f frontend/
kubectl delete -f backend/
kubectl delete -f postgres/
```

## Production Tips

- Use proper secret management
- Add Ingress + HTTPS
- Set resource limits
- Enable monitoring & backups

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.