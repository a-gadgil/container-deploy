# Polyglot Rust-MariaDB Stack (Containerized 3-Tier Architecture)

5-component polyglot Order & Risk-Scoring stack packaged with hardened multi-stage Distroless builds (`distroless/static` for Go, `distroless/cc` for Rust), read-only root filesystems, dropped Linux capabilities, and segmented Docker networks.

## Architecture & Network Segmentation

| Tier | Container | Image | Network(s) | Hardening |
| :--- | :--- | :--- | :--- | :--- |
| Tier 1 (Edge) | `polyglot-rm-nginx` | `nginx:1.27-alpine` | `frontend` (`0.0.0.0:80`) | `read_only`, `cap_drop ALL` + `NET_BIND_SERVICE` |
| Tier 2A (Core API) | `polyglot-rm-go-api` | `polyglot-rm-go-api:1.0.0` | `frontend`, `backend` | `distroless/static` nonroot (`65532`), `read_only`, `cap_drop ALL` |
| Tier 2B (Compute) | `polyglot-rm-rust-api` | `polyglot-rm-rust-api:1.0.0` | `frontend`, `backend` | `distroless/cc` nonroot (`65532`), `read_only`, `cap_drop ALL` |
| Tier 3A (Record Store) | `polyglot-rm-mariadb` | `mariadb:11.4` | `backend` (`internal`) | Isolated internal bridge, auto-init schema + seed |
| Tier 3B (Cache) | `polyglot-rm-redis` | `redis:7-bookworm` | `backend` (`internal`) | Isolated internal bridge, AOF persistence |

## Quick Start

1. Copy environment template and bring up the stack:
   `cp .env.example .env && docker compose up -d --build`
2. Tear down (preserving volumes):
   `docker compose down`
