# Provider Feedback Portal

A full-stack feedback management system built with React, Spring Boot, Kafka, and PostgreSQL.

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

## Quick Start

### Starting All Services

From the root directory (`joshua-devin-final/`):

```bash
# Start all services (infrastructure + applications)
docker compose --profile app up -d

# View logs from all services
docker compose --profile app logs -f

# View logs from a specific service
docker compose logs -f feedback-api
docker compose logs -f analytics-consumer
docker compose logs -f frontend-ui
```

**Note**: The `--profile app` flag is required because the application services are configured with `profiles: ["app"]` in docker-compose.yml.

### Starting Only Infrastructure

For local development (running services outside Docker):

```bash
# Only starts Kafka, Postgres, and Kafka UI
docker compose up -d
```

## Service URLs & Testing

### 1. Frontend UI
- **URL**: `http://localhost:5173`
- **Features**:
  - Submit Feedback form (memberId, providerName, rating 1-5, optional comment)
  - My Feedback page (filter by memberId)
  - Client-side validation (rating 1-5, comment max 200 chars)

### 2. Swagger/OpenAPI Documentation
- **Swagger UI**: `http://localhost:8082/swagger-ui.html`
- **API Docs (JSON)**: `http://localhost:8082/api-docs`

**Available Endpoints**:
- `POST /api/v1/feedback` - Submit new feedback
- `GET /api/v1/feedback/{id}` - Get feedback by UUID
- `GET /api/v1/feedback?memberId={id}` - Get feedback by member ID
- `GET /api/v1/health` - Health check

**Example POST Request**:
```json
{
  "memberId": "m-123",
  "providerName": "Dr. Smith",
  "rating": 4,
  "comment": "Great experience."
}
```

### 3. Kafka Testing

**Kafka UI**:
- **URL**: `http://localhost:8000`
- View topics, messages, consumer groups, and broker information
- Check topic: `feedback-submitted`

**Verify Kafka Flow**:
1. Submit feedback via frontend or Swagger
2. Check Kafka UI at `http://localhost:8000`:
   - Navigate to Topics → `feedback-submitted`
   - View messages in the topic
3. Check consumer logs:
```bash
docker compose logs -f analytics-consumer
```

Expected log output:
```
Received feedback event id=... memberId=... provider=... rating=... schemaVersion=1
```

**Direct Kafka Commands** (optional):
```bash
# List topics
docker exec -it kafka kafka-topics --bootstrap-server localhost:9092 --list

# View messages in feedback-submitted topic
docker exec -it kafka kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic feedback-submitted \
  --from-beginning
```

## End-to-End Test Flow

1. **Start all services**:
   ```bash
   docker compose --profile app up -d
   ```

2. **Wait for services to be healthy**:
   ```bash
   docker compose --profile app ps
   ```

3. **Test Frontend**:
   - Open `http://localhost:5173`
   - Submit feedback with valid data
   - View feedback by memberId

4. **Test Swagger**:
   - Open `http://localhost:8082/swagger-ui.html`
   - Test all endpoints interactively

5. **Verify Kafka**:
   - Open `http://localhost:8000` (Kafka UI)
   - Check `feedback-submitted` topic for messages
   - Check consumer logs: `docker compose logs analytics-consumer`

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

```
joshua-devin-final/
├── docker-compose.yml          # Orchestrates all services
├── documentation/              # Project documentation
└── README.md                   # This file

../tsg-9.27-devinjosh-feedback-api/          # Spring Boot API
../tsg-9.27-devinjosh-feedback-analytics-consumer/  # Kafka Consumer
../tsg-9.27-devinjosh-frontend-feedback-ui/  # React Frontend
```

## Additional Resources

- See individual service READMEs for local development instructions
- API documentation available at `/swagger-ui.html` when API is running
- Kafka UI available at `http://localhost:8000` for message inspection
```
