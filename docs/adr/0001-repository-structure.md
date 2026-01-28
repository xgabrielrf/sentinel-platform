# ADR-0001: Repository structure (monorepo)

## Status
Accepted

## Context
Sentinel Platform will evolve over time (possibly modular monolith → microservices).
We need a structure that supports documentation, infra, and services with clear boundaries.

## Decision
Adopt a monorepo layout:
- `docs/` for architecture and ADRs
- `services/` for runtime components
- `infra/` for deployment assets
- `scripts/` for automation

## Consequences
- + Single source of truth
- + Easier shared tooling and CI
- - Requires discipline for module boundaries

## Alternatives Considered
- Multi-repo: rejected due to higher operational overhead early stage.
