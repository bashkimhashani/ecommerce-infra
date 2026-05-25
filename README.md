# Vendora Infrastructure

This repository contains the Docker Compose setup for the Vendora distributed
ecommerce project. It starts the frontend, backend, PostgreSQL, Redis, Celery
worker, and Celery Beat services together.

## Project Overview

Vendora is split into three sibling repositories:

- `ecommerce-backend` - Django REST API
- `ecommerce-frontend` - Vue 3 frontend
- `ecommerce-infra` - Docker Compose, environment configuration, and local orchestration

The frontend communicates with the backend over REST. The backend stores data in
PostgreSQL, uses Redis for cache and Celery coordination, and runs Celery workers
for background jobs.

## Tech Stack

| Area | Technology |
| --- | --- |
| Frontend | Vue 3, Vite, Pinia, Vue Router, Tailwind CSS |
| Backend | Python, Django, Django REST Framework |
| Database | PostgreSQL 15 |
| Cache and broker | Redis 7 |
| Background jobs | Celery worker and Celery Beat |
| API docs | drf-spectacular, Swagger UI, ReDoc |
| Payments | Stripe |
| AI | OpenAI API |
| Containers | Docker and Docker Compose |

## Services

| Service | Purpose | Local URL or port |
| --- | --- | --- |
| `frontend` | Vue/Vite development server | `http://localhost:5173` |
| `web` | Django API server | `http://localhost:8000` |
| `db` | PostgreSQL database | host `5433`, container `5432` |
| `redis` | Redis cache, broker, result backend | `6379` |
| `celery_worker` | Async task worker | internal |
| `celery_beat` | Scheduled task runner | internal |

## Requirements

- Docker Desktop
- Git

## First Time Setup

Clone all repositories into the same parent folder:

```bash
git clone https://github.com/bashkimhashani/ecommerce-backend.git
git clone https://github.com/bashkimhashani/ecommerce-frontend.git
git clone https://github.com/bashkimhashani/ecommerce-infra.git
```

Move into this repository:

```bash
cd path/to/Vendora/ecommerce-infra
```

Create the local environment file:

```bash
cp .env.example .env
```

Build and start all services:

```bash
docker compose up --build -d
```

Apply migrations and seed demo data:

```bash
docker compose exec web python manage.py migrate
docker compose exec web python manage.py seed_demo_data
```

## Local URLs

- Frontend: `http://localhost:5173`
- Backend: `http://localhost:8000`
- Swagger UI: `http://localhost:8000/api/docs/`
- ReDoc: `http://localhost:8000/api/redoc/`
- OpenAPI schema: `http://localhost:8000/api/schema/`
- Django admin: `http://localhost:8000/admin/`

Use `http://localhost:5173` for the frontend because local CORS is configured
for that origin.

## Demo Accounts

After running `seed_demo_data`, these users are available:

```text
admin@example.com
vendor@example.com
gaming.vendor@example.com
office.vendor@example.com
customer@example.com
```

Password:

```text
DemoPass123!
```

## Daily Commands

Start services:

```bash
docker compose up -d
```

Stop services:

```bash
docker compose down
```

Rebuild services:

```bash
docker compose up --build -d
```

Show service status:

```bash
docker compose ps
```

Backend logs:

```bash
docker compose logs -f web
```

Frontend logs:

```bash
docker compose logs -f frontend
```

Run migrations:

```bash
docker compose exec web python manage.py migrate
```

Refresh demo data:

```bash
docker compose exec web python manage.py seed_demo_data
```

Run backend tests:

```bash
docker compose exec web python manage.py test
```

Run frontend tests:

```bash
docker compose exec frontend npm run test
```

## Environment File

The `.env` file is loaded by Docker Compose. Important variables include:

```env
POSTGRES_DB=ecommerce_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
CORS_ALLOWED_ORIGINS=http://localhost:5173
REDIS_URL=redis://redis:6379/0
FRONTEND_URL=http://localhost:5173
STRIPE_SECRET_KEY=sk_test_replace_me
STRIPE_PUBLISHABLE_KEY=pk_test_replace_me
STRIPE_WEBHOOK_SECRET=whsec_replace_me
OPENAI_API_KEY=
AWS_STORAGE_BUCKET_NAME=
```

Do not commit real API keys or secrets.

## Reset Local Data

To remove containers and database volumes:

```bash
docker compose down -v
docker compose up --build -d
docker compose exec web python manage.py migrate
docker compose exec web python manage.py seed_demo_data
```

## Troubleshooting

If the frontend shows `Failed to fetch`, check that the backend is running:

```bash
docker compose ps
docker compose logs -f web
```

If API errors mention missing columns, run migrations:

```bash
docker compose exec web python manage.py migrate
```

If products are missing images, reseed demo data:

```bash
docker compose exec web python manage.py seed_demo_data
```

If dependency files changed, rebuild:

```bash
docker compose up --build -d
```

## Git Workflow

Each repository has its own Git history. Pull or push inside the repository that
changed:

```bash
cd path/to/Vendora/ecommerce-infra
git checkout main
git pull origin main
git checkout -b feature/yourname/task-name
```

Commit and push:

```bash
git add .
git commit -m "type: describe the change"
git push origin feature/yourname/task-name
```
