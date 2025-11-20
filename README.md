# Provider Feedback Portal

A full-stack feedback management system built with React, Spring Boot, Kafka, and PostgreSQL.

### This project contains Submodules


## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Service URLs](#service-urls)
- [Running Tests](#running-tests)
- [End-to-End Test Flow](#end-to-end-test-flow)
- [Stopping Services](#stopping-services)
- [Troubleshooting](#troubleshooting)
- [Project Structure](#project-structure)
- [Additional Resources](#additional-resources)

---

## Architecture Overview

- **Frontend UI**: React app (Vite) - Port `5173`
- **Feedback API**: Spring Boot REST API - Port `8082` (Swagger enabled)
- **Analytics Consumer**: Spring Boot Kafka consumer (logs events)
- **Kafka**: Message broker - Port `9092`
- **Kafka UI**: Web interface - Port `8000`
- **PostgreSQL**: Database - Port `5432`

## Prerequisites

- Docker and Docker Compose
- Java 21 (for local development)
- Node.js 20+ (for local frontend development)

**Clone this repository**

This project consists of **one repository** that contains **submodules**. When cloning this repo you must use a <u>recursive</u> command.
```bash
git clone --recursive https://github.com/York-Solutions-B2E/joshua-devin-final.git
```
This will set up the entire repo for you.

If you already cloned without --recursive run:

```bash
git submodule update --init --recursive
```

**Reference repositories** (don't clone these, the recursive command will do that for you):

Feedback-ui: https://github.com/York-Solutions-B2E/tsg-9.27-devinjosh-frontend-feedback-ui.git

Feedback-api: https://github.com/York-Solutions-B2E/tsg-9.27-devinjosh-feedback-api.git

Feedback-analytics-consumer: https://github.com/York-Solutions-B2E/tsg-9.27-devinjosh-feedback-analytics-consumer.git



## Quick Start

### Starting All Services

From the root directory (`joshua-devin-final/`):

```bash
cd joshua-devin-final

# Start all services (infrastructure + applications)
docker compose --profile app up -d

# View logs from all services
docker compose --profile app logs -f

# View logs from a specific service
docker compose logs -f feedback-api
docker compose logs -f analytics-consumer
docker compose logs -f frontend-ui
```

**Starting Only Infrastructure**

For local development (running services outside Docker):

```bash
# Only starts Kafka, Postgres, and Kafka UI
docker compose up -d
```


**Note**: The `--profile app` flag is required because the application services are configured with `profiles: ["app"]` in docker-compose.yml.

- If needed run `docker compose --profile app up -d --build` to rebuild the container.

## Service URLs

- **Frontend UI**: http://localhost:5173
- **API Swagger**: http://localhost:8082/swagger-ui.html
- **API Docs (JSON)**: http://localhost:8082/api-docs
- **Kafka UI**: http://localhost:8000

**API Endpoints**:
- `POST /api/v1/feedback` - Submit new feedback
- `GET /api/v1/feedback/{id}` - Get feedback by UUID
- `GET /api/v1/feedback?memberId={id}` - Get feedback by member ID
- `GET /api/v1/health` - Health check

## Running Tests

**If you run into any permission errors during testing please run this command:**
```bash
chmod +x mvnw
```

### Feedback API (`tsg-9.27-devinjosh-feedback-api`)

**Test Files**:
- `FeedbackServiceTest.java` - Unit tests for service layer (validation, business logic)
- `FeedbackControllerTest.java` - Integration tests for REST endpoints

**Run Tests**:
```bash
cd tsg-9.27-devinjosh-feedback-api
./mvnw clean test
```

**Expected Output**:
```
[INFO] Tests run: 18, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
```

**Test Coverage**:
-  Service validation (memberId, providerName, rating, comment)
-  Happy path feedback creation
-  GET by ID and memberId
-  Error handling (not found, validation errors)
-  Controller endpoints (POST, GET)
-  JSON serialization/deserialization

### Analytics Consumer (`tsg-9.27-devinjosh-feedback-analytics-consumer`)

**Test Files**:
- `FeedbackEventListenerTest.java` - Unit tests for Kafka event listener
- `FeedbackAnalyticsConsumerApplicationTests.java` - Application context test

**Run Tests**:
```bash
cd tsg-9.27-devinjosh-feedback-analytics-consumer
./mvnw clean test
```

**Expected Output**:
```
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
```

**Test Coverage**:
- ✅ Event listener logging for valid payloads
- ✅ Warning logs for comments exceeding length limit
- ✅ JSON round-trip serialization/deserialization
- ✅ Application context loading

### Frontend UI (`tsg-9.27-devinjosh-frontend-feedback-ui`)

**Test Files**: None currently implemented

**Run Linting**:
```bash
cd tsg-9.27-devinjosh-frontend-feedback-ui
npm run lint
```

**Note**: Frontend tests can be added using React Testing Library or Vitest if needed.

## End-to-End Test Flow

1. **Start all services**: `docker compose --profile app up -d`
2. **Wait for services**: `docker compose --profile app ps` (verify all healthy)
3. **Test Frontend**: Open http://localhost:5173, submit feedback, view by memberId
    - If you want to search by Feedback ID instead, you will have to find the UUID in Kafbat UI
4. **Test Swagger**: Open http://localhost:8082/swagger-ui.html, test endpoints interactively
5. **Verify Kafka**: 
   - Open http://localhost:8000 (Kafka UI), check `feedback-submitted` topic
   - Check consumer logs: `docker compose --profile app logs analytics-consumer`
   - Expected log: `Received feedback event id=... memberId=... provider=... rating=... schemaVersion=1`

## Stopping Services

```bash
# Stop all services
docker compose --profile app down

# Stop and remove volumes (clean slate)
docker compose --profile app down -v
```

## Troubleshooting

- **Services not starting**: Check logs with `docker compose --profile app logs`
- **Frontend can't connect to API**: Ensure API is running on port 8082. Frontend defaults to `http://localhost:8082/api/v1`
- **Kafka connection issues**: Verify Kafka health with `docker compose ps kafka`
- **Database connection**: Ensure Postgres is healthy before API starts (configured with `depends_on`)


## Project Structure
**Current Repo**
```
joshua-devin-final/
├── docker-compose.yml # Orchestrates all services
├── documentation/ # Project documentation
├── README.md # This file
│
├── tsg-9.27-devinjosh-feedback-api/ # Submodule: Spring Boot API
│ ├── src/main/java/
│ ├── src/main/resources/
│ ├── Dockerfile
│ ├── pom.xml
│ └── README.md
│
├── tsg-9.27-devinjosh-feedback-analytics-consumer/ # Submodule: Kafka Consumer
│ ├── src/main/java/
│ ├── src/main/resources/
│ ├── Dockerfile
│ ├── pom.xml
│ └── README.md
│
└── tsg-9.27-devinjosh-frontend-feedback-ui/ # Submodule: React Frontend
├── src/
├── Dockerfile
├── package.json
└── README.md
```
## Additional Resources

- See individual service READMEs for local development instructions
- API documentation available at `/swagger-ui.html` when API is running
- Kafka UI available at `http://localhost:8000` for message inspection
```
