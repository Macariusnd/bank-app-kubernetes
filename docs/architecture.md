# Architecture

## High-level flow

```
User (browser)
      │
      ▼
Kubernetes Ingress (Nginx)
      │
      ├──► frontend Service ──► React Pod(s)
      │                              │
      │                              │ API calls
      │                              ▼
      └──► backend Service ──► Spring Boot Pod(s)
                                     │
                                     ▼
                              MySQL Database
                         (managed instance, private)
```

## Components

### Frontend (React)
- Containerized React application
- Served as a Kubernetes `Deployment` with a `ClusterIP` Service
- Environment variable `REACT_APP_API_URL` points to the backend service
  (injected via ConfigMap)
- Exposed to the outside world only via the Ingress controller

### Backend (Spring Boot)
- Java REST API built with Spring Boot and Maven
- Containerized and deployed as a Kubernetes `Deployment`
- Reads database connection details from a `ConfigMap` (host, port, db name)
  and credentials from a Kubernetes `Secret` (username, password)
- Exposes its API port only as a `ClusterIP` Service — not reachable directly
  from outside the cluster

### Database (MySQL)
- Managed MySQL instance (e.g. AWS RDS or DigitalOcean Managed Database)
- Sits outside the cluster; only the backend pods can reach it via the
  credentials supplied in the Kubernetes Secret
- Never exposed to the public internet

### Kubernetes Manifests
| File | Purpose |
|---|---|
| `frontend.yaml` | Frontend Deployment |
| `frontend-service.yaml` | Frontend ClusterIP Service |
| `backendapi.yaml` | Backend Deployment |
| `bankend-service.yaml` | Backend ClusterIP Service |
| `backend-ingress.yaml` | Ingress rules routing traffic to frontend/backend |
| `configmap.yaml` | Non-sensitive config (DB host, port, name, API URL) |
| `secret.yaml` | Database credentials (template only — see README) |

## Security considerations
- Database credentials are kept out of source control entirely
- ConfigMap holds only non-sensitive configuration
- No service is exposed via `LoadBalancer` or `NodePort` directly —
  all external traffic goes through the Ingress controller
- The database is on a private network, reachable only from within the cluster
