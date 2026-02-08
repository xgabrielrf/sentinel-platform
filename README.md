# Sentinel Platform

Plataforma de seguros/bancária orientada a eventos (Kafka) com foco em: cotação, risco, apólice, cobrança e auditoria.

## Quickstart (dev local)
Pré-requisitos:
- Docker + Docker Compose

### Subir infraestrutura (Postgres + Kafka)
```bash
cd infra/docker
docker compose up -d
docker compose ps
```

### Derrubar
```bash
cd infra/docker
docker compose down -v
```