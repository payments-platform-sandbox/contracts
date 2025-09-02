# Contracts (`contracts`)

> Shared API and event contracts for the **payments platform**.  
> Provides a single source of truth for REST endpoints and event schemas across all services.  


## 1) Overview
This repository contains **contracts only** (no service code).  
Contracts define how services interact with each other and with external clients.  
They ensure consistency, versioning, and safe evolution of APIs and events.  

**Includes:**
- **OpenAPI specs** — REST API definitions (YAML/JSON)  
- **Avro/Proto schemas** — event and message definitions  
- **Versioned releases** — tagged for compatibility across services  


## 2) Why Contracts Matter
- **Consistency** → all services reference the same schemas.  
- **Clarity** → consumers (clients, external systems) know exactly what to expect.  
- **Tooling** → clients and mocks can be generated automatically.  
- **Versioning** → services can safely evolve while staying compatible.  


## 3) Repository Layout

```
├── openapi/
│   ├── payment-service.yaml       # Payments API (create, refund, cancel)
│   ├── orchestration.yaml         # Orchestration workflows API (if exposed)
│   └── moov-ach-ledger.yaml       # ACH + ledger APIs
│
├── events/
│   ├── payment-created.avsc       # Avro schema for payment events
│   ├── ledger-updated.avsc        # Avro schema for ledger events
│   └── refund-processed.avsc      # Avro schema for refund events
│
└── README.md
```
## 4) Example OpenAPI Snippet
```yaml
openapi: 3.0.1
info:
  title: Payment Service API
  version: 1.0.0
paths:
  /payments:
    post:
      summary: Start a new payment
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PaymentRequest'
      responses:
        '201':
          description: Payment created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'
components:
  schemas:
    PaymentRequest:
      type: object
      properties:
        amount: { type: number }
        currency: { type: string }
        source: { type: string }
        destination: { type: string }
```

5) Versioning
- Contracts follow semantic versioning (v1.0.0, v1.1.0, etc.).
- Breaking changes → major version bump.
- Services should pin to a specific contract version.

6) Usage
- **Services** reference schemas from this repo.
- **CI/CD** can validate service implementation against contract.
- **Clients** (internal or external) can generate code stubs from OpenAPI/Avro.

Example (generate Java client from OpenAPI):

```
openapi-generator-cli generate \
  -i openapi/payment-service.yaml \
  -g java \
  -o ./clients/java/payment-service
```

## 7) Notes
	•	Contracts are not **runnable services**.
	•	Keep all API/event definitions here to avoid drift.
	•	Services should not diverge from contracts without updating this repo first.


## 8) License

MIT
