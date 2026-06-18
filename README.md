# Online Banking App — Deployed on Kubernetes

A full-stack banking application (React frontend, Spring Boot backend, MySQL database)
containerized and deployed to a Kubernetes cluster, with ingress routing, ConfigMaps,
and Kubernetes Secrets for configuration and credential management.

## Overview

This project takes a complete banking application — frontend, backend API, and database —
and deploys it onto Kubernetes following standard production patterns:

- **Frontend**: React application served as a containerized deployment
- **Backend**: Spring Boot REST API, containerized, talking to a managed MySQL database
- **Kubernetes manifests**: Deployments and Services for both tiers, an Ingress resource
  for routing, a ConfigMap for non-sensitive configuration, and a Secret for credentials
- **Database**: Managed MySQL instance (e.g. AWS RDS), accessed securely from within the
  cluster, never exposed publicly

## Tech stack

| Layer | Tools |
|---|---|
| Frontend | React |
| Backend | Java, Spring Boot, Maven |
| Database | MySQL (managed instance) |
| Container orchestration | Kubernetes (Deployments, Services, Ingress, ConfigMaps, Secrets) |
| Containerization | Docker |

## Repo structure

```
.
├── frontend/          # React application source + Dockerfile
├── backend/           # Spring Boot application source + Dockerfile
├── kubernetes/         # Kubernetes manifests (Deployments, Services, Ingress, ConfigMap, Secret template)
├── diagrams/          # Architecture diagram
└── docs/
    └── architecture.md  # Detailed architecture write-up
```

## How it was built

1. Built and containerized the React frontend with a Dockerfile.
2. Built and containerized the Spring Boot backend with a Dockerfile.
3. Provisioned a managed MySQL database to serve as the persistent data layer.
4. Wrote Kubernetes manifests for both tiers: `Deployment` + `Service` for frontend and
   backend, an `Ingress` resource to route external traffic, a `ConfigMap` for
   non-sensitive environment configuration, and a `Secret` for database credentials.
5. Deployed the manifests to a Kubernetes cluster and verified end-to-end connectivity
   from frontend → backend → database.

See [docs/architecture.md](docs/architecture.md) for the full breakdown.

## Configuration and secrets

This repo intentionally does **not** include real credentials or environment values.

- `frontend/.env.example` and `backend/.env.example` show the required environment
  variables without real values — copy to `.env` and fill in your own.
- `kubernetes/secret.yaml` is a **template** showing the expected structure of the
  Kubernetes Secret. In a real deployment, secrets should be supplied via a secrets
  manager (e.g. AWS Secrets Manager, HashiCorp Vault) or injected at deploy time via
  CI/CD — never committed to source control.
- `kubernetes/configmap.yaml` values are placeholders; replace with your own database
  host/port/name.

## What I'd improve next

- Move secret management to an external secrets manager instead of static Kubernetes Secrets
- Add resource requests/limits to the Deployments for better cluster scheduling
- Add a CI/CD pipeline to build, test, and deploy on push
- Add liveness/readiness probes to both Deployments
