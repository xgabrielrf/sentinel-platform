# Sentinel Platform — Architecture

This document is the architectural north star for the Sentinel Platform.  
It evolves together with the codebase and is meant to support:
- product & engineering alignment
- secure-by-design decisions
- operational excellence (SRE/Observability)
- regulated environments (insurance/banking context)

---

## 1) Purpose & Scope

**Sentinel Platform** is a modular backend platform designed for regulated domains (insurance/banking), with:
- secure authentication and authorization
- API-first integrations
- auditable workflows
- strong observability and resilience patterns

**Out of scope (for now):**
- UI/front-end (we’ll keep it API-driven)
- full-blown data lake/BI (only operational data and audit trails)

---

## 2) C4 Model

### 2.1 C1 — System Context

**Actors**
- **Customer / End User**: consumes business capabilities through client channels
- **Partner Systems**: brokers, external integrators, Open Insurance ecosystem
- **Operations**: NOC/SRE/Support monitoring and incident response
- **Compliance/Audit**: evidence and traceability

**System**
- **Sentinel Platform**: provides protected APIs, workflows, and auditability

**External dependencies (typical)**
- Identity Provider (IdP) / STS
- Messaging/Events (optional)
- Observability stack (logs, metrics, traces)
- Notification providers (email/SMS/whatsapp) — optional

---

### 2.2 C2 — Containers (logical)

> This is the target shape. We can start as a modular monolith and evolve into microservices.

**API Gateway / BFF**
- Single entry point
- Rate limiting, request validation, auth delegation
- Routes to internal services

**Auth & Identity**
- Token validation, session policies
- RBAC/ABAC decisions
- Integration with STS/IdP (OIDC/OAuth2)

**Core Domain Services**
- Policy/Customer/Claims/Orders (names will evolve)
- Business rules
- Strong audit trail on critical actions

**Audit & Compliance**
- Immutable audit log (append-only)
- Evidence export for audit

**Data Stores**
- OLTP relational database (PostgreSQL recommended)
- Optional cache (Redis)
- Optional event store / message broker (Kafka/RabbitMQ)

---

### 2.3 C3 — Components (example decomposition)

**Auth Service**
- Token Validator
- Policy Enforcement Point (PEP)
- Role/Permission Resolver
- Session/Risk Controls (MFA hooks, device checks, throttling)

**Domain Service (example: Policy)**
- API Layer (Controllers)
- Application Layer (Use Cases)
- Domain Layer (Entities, Value Objects, Rules)
- Infrastructure Layer (DB adapters, external clients)

**Audit**
- Audit Event Builder
- Audit Sink (DB append-only / external storage)
- Evidence Exporter

---

### 2.4 C4 — Code (project structure)

Proposed repository layout (target):
```
docs/
architecture/
adr/
services/
gateway/
auth-service/
policy-service/
infra/
docker/
k8s/
terraform/
scripts/
```

> We will keep boundaries clear with module-level ownership and clean architecture patterns.

---

## 3) Core Architectural Principles

1. **Secure by default**
   - deny-by-default authorization
   - least privilege and explicit permissions
2. **API-first**
   - stable contracts, versioning strategy, backward compatibility
3. **Observable by design**
   - every request is traceable end-to-end
   - audit and security events are first-class
4. **Regulated-ready**
   - immutable audit log for critical actions
   - evidence and retention policies
5. **Evolutionary architecture**
   - start simple; preserve modularity so scaling is a choice, not a rewrite

---

## 4) Security Baseline (Target)

### 4.1 Identity & Access
- OAuth2/OIDC with an external IdP/STS
- JWT validation at the edge (Gateway) + defense-in-depth in services
- RBAC for roles + ABAC for business constraints (tenant, channel, risk)

### 4.2 Secrets & Key Management
- never commit secrets
- environment-based config
- plan: secrets manager (cloud-native or vault)

### 4.3 Transport Security
- TLS everywhere
- mTLS internal (optional, later)

### 4.4 OWASP & AppSec practices
- input validation at the edge
- structured error responses (no sensitive leakage)
- dependency scanning & code scanning (CodeQL)

---

## 5) Observability (Logs / Metrics / Traces)

### 5.1 Logs
- structured JSON logs
- correlation IDs (trace_id / span_id)
- security events separated (auth failures, suspicious patterns)

### 5.2 Metrics
- RED metrics at minimum:
  - Rate
  - Errors
  - Duration
- saturation indicators (CPU/memory/thread pools/connections)

### 5.3 Traces
- OpenTelemetry instrumentation
- propagate trace context across services
- sampling policy defined by environment

### 5.4 Operational readiness
- health endpoints:
  - liveness
  - readiness
- SLOs (future):
  - availability and latency per critical endpoint
- incident playbooks references (future)

---

## 6) Compliance Notes (LGPD / Open Insurance)

> Not legal advice. This section documents engineering controls that support compliance.

### 6.1 LGPD-aligned controls (engineering)
- data minimization (collect only what is needed)
- clear data classification (PII vs non-PII)
- access logging and auditability
- encryption in transit and at rest (target)
- retention and deletion strategy (target)
- separation of duties (roles for ops vs dev vs audit)

### 6.2 Open Insurance (engineering posture)
- API security (OAuth2/OIDC)
- consent and purpose tracking (target)
- robust auditing of data access
- non-repudiation signals (audit + trace + time sync)

---

## 7) Architecture Decision Records (ADRs)

All major decisions must be captured as ADRs under `docs/adr/`.

Examples:
- ADR-0001: Monorepo layout
- ADR-0002: Maven vs Gradle
- ADR-0003: Spring Boot baseline + modules
- ADR-0004: PostgreSQL as OLTP
- ADR-0005: OpenTelemetry adoption

---

## 8) Next Steps (Backlog)

- Define minimal “Walking Skeleton” services
  - gateway + auth + one domain service + audit
- Create CI for Java build + tests
- Enable CodeQL
- Create ADR template + first ADRs
- Define API versioning & error contract
