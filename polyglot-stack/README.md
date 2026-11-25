# Polyglot Stack (Containerized 3-Tier Architecture)

5-component polyglot Order & Risk-Scoring stack orchestrated with Docker Compose using multi-stage builds (Distroless nonroot for Go, slim non-root for Python), read-only root filesystems, healthcheck-gated startup ordering, and segmented frontend/backend networks.

## Architecture & Network Contract

| Tier | Service | Image / Base | Exposed Port | Networks |
| :--- | :--- | :--- | :--- | :--- |
| Tier 1 (Edge) | nginx | nginx:1.27-bookworm | 0.0.0.0:80->80/tcp | frontend |
| Tier 2A (Core API) | go-api | distroless/static-debian12:nonroot | 8090/tcp (internal) | frontend, backend |
| Tier 2B (Compute) | python-api | python:3.12-slim-bookworm | 8091/tcp (internal) | frontend, backend |
| Tier 3A (Record Store) | postgres | postgres:16-bookworm | 5432/tcp (internal) | backend (internal: true) |
| Tier 3B (Cache) | redis | redis:7-bookworm | 6379/tcp (internal) | backend (internal: true) |

## Lifecycle Commands

1. Build and start the full stack:
   docker compose up -d --build

2. Tear down containers (preserving volumes):
   docker compose down
