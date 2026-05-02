# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Diffgram is an AI Datastore for Schemas, BLOBs, and Predictions. It provides data labeling capabilities for various media types (images, video, 3D, text, audio, geospatial) and integrates with ML workflows.

## Architecture

The project uses a microservices architecture:

### Backend Services
- **default** (port 8080): Main Flask API server - handles HTTP requests, authentication, permissions, and business logic
- **walrus** (port 8082): File handling service - manages file storage across multiple cloud providers
- **eventhandlers** (port 8086): Background task processor - handles async tasks via RabbitMQ
- **local_dispatcher** (port 8085): Task dispatcher for local execution

### Frontend
- Vue.js 2 application with Vuetify 2 component library
- Vuex for state management
- Vue Router for navigation
- Located in `frontend/` directory

### Infrastructure
- **Database**: PostgreSQL with SQLAlchemy ORM (models in `shared/database/`)
- **Message Queue**: RabbitMQ for async task processing
- **Storage Providers**: S3, Google Cloud Storage, Azure Blob, MinIO (adapters in `shared/data_tools_core*.py`)

## Common Commands

### Backend Development
```bash
# Setup virtual environment and install dependencies
make setup-env-backend

# Run background services (database, rabbitmq, minio)
make run-bg-services

# Run individual services
make run-default      # Main API server
make run-walrus      # File handling service
make run-eventhandlers  # Background tasks
make run-dispatcher  # Local dispatcher
```

### Frontend Development
```bash
cd frontend

# Install dependencies
yarn install

# Run development server (hot reload)
yarn run dev

# Build for production
yarn run build

# Run unit tests
yarn run test:unit

# Run unit tests with coverage
yarn run test:unit-verbose

# Run E2E tests (Cypress)
yarn run e2e

# Lint code
yarn run lint
yarn run lint:fix  # Auto-fix linting issues
```

### Testing
Backend tests require setting `DIFFGRAM_SYSTEM_MODE=testing` and `UNIT_TESTING_DATABASE_URL` environment variables. Tests use pytest and create/destroy a test database automatically.

```bash
# Run a specific test file
cd default && python -m pytest tests/methods/annotation/test_instance_template_list.py -v

# Run tests in a directory
cd default && python -m pytest tests/methods/task/task/
```

## Configuration

Configuration is environment-based with settings in `shared/settings/settings.py`. Key environment variables:

- `DIFFGRAM_SYSTEM_MODE`: Set to `sandbox`, `testing`, or `production`
- `DATABASE_URL`: PostgreSQL connection string
- `DIFFGRAM_STATIC_STORAGE_PROVIDER`: Storage backend (s3, gcs, azure, minio)
- Storage provider credentials (AWS_ACCESS_KEY, GCP_SERVICE_ACCOUNT, etc.)

## Code Organization

### Backend Structure
- `default/methods/`: API endpoint handlers organized by domain (annotation, task, project, user, etc.)
- `shared/database/`: SQLAlchemy models organized by entity (project, task, annotation, user, etc.)
- `shared/auth/`: Authentication providers (Cognito, Keycloak, OIDC)
- `shared/helpers/`: Utility functions (permissions, session, etc.)
- `shared/utils/`: General utilities
- `shared/query_engine/`: Database query building

### Frontend Structure
- `frontend/src/components/`: Vue components organized by feature
- `frontend/src/services/`: API service layers
- `frontend/src/store.js`: Vuex store configuration
- `frontend/src/router/`: Vue Router configuration

## Key Patterns

### API Endpoints
Routes are defined in `default/routes_init.py` and implemented in `default/methods/`. Each domain has its own directory with route handlers.

### Database Models
Models use SQLAlchemy and are defined in `shared/database/`. Each entity (Project, Task, etc.) has its own module with the model class.

### Permissions
Permission checking is handled in `shared/helpers/permissions.py` using a role-based access control system.

## Documentation Standards

All generated documentation files (technical design, architecture proposals, etc.) MUST follow Feishu (飞书) compatible Markdown format:

- File format: `.md` with standard Markdown syntax
- Flowcharts/sequence diagrams: Use Mermaid `sequenceDiagram` syntax wrapped in code blocks (飞书 Markdown renders these natively)
- Tables: Use standard Markdown table syntax (`| col1 | col2 |`)
- Code blocks: Use triple backticks with language identifier (```sql, ```go, ```json)
- Headings: Use `#` hierarchy, start from `##` for sections within a document
- Language: Technical documents default to Chinese (中文), code and field names keep English
