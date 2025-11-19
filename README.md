# Provider Feedback Portal

A full-stack feedback management system built with React, Spring Boot, Kafka, and PostgreSQL.


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

**Clone All Repositories**

This project consists of **four separate repositories** that must be cloned as **sibling directories**. The Docker Compose file uses relative paths, so the directory structure is critical.

**Create a parent directory** for all repositories:

```bash
# Create a directory for the project
mkdir Member_Feedback_Final
cd Member_Feedback_Final
```

**Clone all four repositories** (replace with your actual GitHub URLs):

```bash
# Clone the Docker orchestration repository
git clone <your-github-url>/joshua-devin-final.git

# Clone the frontend repository
git clone <your-github-url>/tsg-9.27-devinjosh-frontend-feedback-ui.git

# Clone the API repository
git clone <your-github-url>/tsg-9.27-devinjosh-feedback-api.git

# Clone the analytics consumer repository
git clone <your-github-url>/tsg-9.27-devinjosh-feedback-analytics-consumer.git
```


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
├── docker-compose.yml          # Orchestrates all services
├── documentation/              # Project documentation
└── README.md                   # This file

../tsg-9.27-devinjosh-feedback-api/          # Spring Boot API
../tsg-9.27-devinjosh-feedback-analytics-consumer/  # Kafka Consumer
../tsg-9.27-devinjosh-frontend-feedback-ui/  # React Frontend
```
**Comprehensive Repo**
```bash
Member_Feedback_Final/
│
├── joshua-devin-final/ # Docker Orchestration Hub
│ ├── docker-compose.yml # Main orchestration file
│ ├── README.md # This file
│ └── documentation/ # Project documentation
│ ├── Spec_Provider_Feedback_Portal.md # Original specification
│ └── Implementation_Plan.md # Development plan
│
├── tsg-9.27-devinjosh-frontend-feedback-ui/ # React Frontend
│ ├── src/ # React source code
│ ├── Dockerfile # Frontend container image
│ ├── package.json # Node dependencies
│ └── README.md # Frontend-specific docs
│
├── tsg-9.27-devinjosh-feedback-api/ # Spring Boot API
│ ├── src/main/java/ # Java source code
│ ├── src/main/resources/ # Configuration files
│ ├── Dockerfile # API container image
│ ├── pom.xml # Maven dependencies
│ └── README.md # API-specific docs
│
└── tsg-9.27-devinjosh-feedback-analytics-consumer/ # Kafka Consumer
├── src/main/java/ # Java source code
├── src/main/resources/ # Configuration files
├── Dockerfile # Consumer container image
├── pom.xml # Maven dependencies
└── README.md # Consumer-specific docs
```
## Additional Resources

- See individual service READMEs for local development instructions
- API documentation available at `/swagger-ui.html` when API is running
- Kafka UI available at `http://localhost:8000` for message inspection
```
