# Provider Feedback Portal MVP Plan

## Day 1 – Foundations
- **setup-repos**  
  - Create repos `frontend-feedback-ui`, `feedback-api`, `feedback-analytics-consumer`.
  - Apply shared tooling: lint configs, formatter, test scripts, `.editorconfig`.
- **arch-decisions**  
  - Capture ADR covering architecture, data flow, validation rules, Kafka contract, env matrix.
  - Store ADR in `/docs` of a shared location.
- **schema-contracts**  
  - Finalize API request/response DTOs, Postgres DDL, Kafka event JSON contract.
  - Document in `docs/contracts.md`.

## Day 2 – Backends Scaffolding
- **api-scaffold (Dev A)**  
  - Generate Spring Boot project; add Postgres datasource config and Flyway baseline.
  - Establish package layout (`controllers`, `services`, `repositories`, `dtos`, `messaging`).
  - Stub controllers/services/repositories with TODO notes.
- **consumer-scaffold (Dev B)**  
  - Create Spring Boot Kafka consumer project; configure topic, bootstrap servers, serializers.
  - Add `HealthController` and listener skeleton.
- **shared-libs**  
  - Decide on shared module or published artifact for `FeedbackEvent`/DTOs.
  - Document versioning and publishing strategy.

## Day 3 – Validation & Listener Core
- **api-validation (Dev A)**  
  - Implement service-layer validation per spec with unit tests.
- **consumer-listener (Dev B)**  
  - Implement Kafka listener with `JsonDeserializer`, structured logging, unit tests.
- **infra-config**  
  - Define `application-local.yml` for API & consumer; align env vars and secrets placeholders.

## Day 4 – API CRUD & Kafka Publish
- **api-crud (Dev A)**  
  - Implement `POST /feedback`, `GET /feedback/{id}` with transactional save and Kafka publish.
  - Add MockMvc tests for happy path and validation errors.
- **api-member-query (Dev A)**  
  - Design service/repository for `GET /feedback?memberId=`; stub tests.
- **consumer-error (Dev B)**  
  - Enhance listener error handling (DLQ/log strategy); add metrics hooks if time permits.
- **kafka-dev-env (Dev B)**  
  - Draft docker-compose fragment for Kafka/Zookeeper; provide topic creation script.

## Day 5 – Integration Wiring
- **api-member-complete (Dev A)**  
  - Finish memberId query implementation with tests.
- **kafka-integration (Dev B)**  
  - Configure Spring Kafka producer/consumer properties, JSON serialization; share configs.
- **compose-base (Pair)**  
  - Build root `docker-compose.yaml` for Postgres, Kafka, API, consumer with health checks.
  - Validate `docker compose up` builds backend services.
- **loop-smoke (Pair)**  
  - Manual cURL to `POST /feedback`, confirm DB row and consumer log entry.

## Day 6 – React Foundations & UI Wiring
- **frontend-scaffold (Dev B)**  
  - Create React project (Vite/CRA), configure routing, shared UI components, axios client.
- **form-implementation (Dev B)**  
  - Build feedback form with client-side validation, success/error states, loading handling.
- **api-hardening (Dev A)**  
  - Finalize error responses, add controller advice tests, enforce Jackson unknown-field rejection.
- **docs-update**  
  - Record API endpoints and validation matrix in README drafts.

## Day 7 – End-to-End Polish
- **list-view (Dev B)**  
  - Implement “My Feedback” list with memberId search, rendering states, integration tests.
- **api-health-tests (Dev A)**  
  - Add `/health` endpoint, Testcontainers-based repository/Kafka tests, validate publish flow.
- **e2e-run (Pair)**  
  - Run full docker-compose stack; demonstrate UI → API → DB → Kafka → consumer; capture evidence for demo.

## Day 8 – Hardening & Docs
- **qa-bugfix (Pair)**  
  - Triage issues, tighten logging, add metrics placeholders, ensure all tests pass.
- **readme-finalize**  
  - Polish READMEs with setup/run/test instructions, sample requests, troubleshooting, env tables.
- **demo-prep**  
  - Script demo narrative, prep validation error demo, cache test commands, note stretch goals.