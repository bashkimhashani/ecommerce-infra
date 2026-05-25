# Vendora Infrastructure

Vendora Infrastructure contains the Docker Compose setup for running the full
Vendora ecommerce platform locally. It coordinates the frontend, backend,
database, cache, and background workers so the team can develop against the same
local environment.

This repository is one part of the Vendora system:

```text
Vendora/
  ecommerce-backend/    Django REST API
  ecommerce-frontend/   Vue customer and vendor interface
  ecommerce-infra/      Docker Compose and local environment orchestration
```

## Platform Overview

Vendora is a multi-tenant technology ecommerce platform. It includes:

- Customer catalog browsing, search, filtering, cart, checkout, and order history
- Vendor inventory, dashboard, order management, exports, and analytics workflows
- Tenant-aware backend data isolation
- Redis-backed cart/cache behavior
- Celery workers for background jobs and scheduled tasks
- Stripe payment integration
- AI chat and sales insight features
- Swagger UI and ReDoc API documentation

## Services

| Service | Purpose | Local URL or port |
| --- | --- | --- |
| `frontend` | Vue/Vite development server | `http://localhost:5173` |
| `web` | Django API server | `http://localhost:8000` |
| `db` | PostgreSQL database | host `5433`, container `5432` |
| `redis` | Redis cache, Celery broker, result backend | `6379` |
| `celery_worker` | Executes asynchronous jobs | internal |
| `celery_beat` | Runs scheduled jobs | internal |

## Tech Stack

| Area | Technology |
| --- | --- |
| Frontend | Vue 3, Vite, Pinia, Vue Router, Tailwind CSS |
| Backend | Python, Django, Django REST Framework |
| Database | PostgreSQL 15 |
| Cache and broker | Redis 7 |
| Background jobs | Celery worker, Celery Beat |
| Payments | Stripe |
| AI | OpenAI API |
| API docs | drf-spectacular, Swagger UI, ReDoc |
| Runtime | Docker, Docker Compose |

## Requirements

- Docker Desktop
- Git

## First-Time Setup

Clone all repositories into the same parent folder:

```bash
git clone https://github.com/bashkimhashani/ecommerce-backend.git
git clone https://github.com/bashkimhashani/ecommerce-frontend.git
git clone https://github.com/bashkimhashani/ecommerce-infra.git
```

Move into this repository and create the local environment file:

```bash
cd path/to/Vendora/ecommerce-infra
cp .env.example .env
```

Build the containers, apply migrations, and seed demo data:

```bash
docker compose up --build -d
docker compose exec web python manage.py migrate
docker compose exec web python manage.py seed_demo_data
```

The seed command prepares the local demo tenant, products, product images,
inventory, orders, and users. Credentials should be shared through the team
channel or read from the seed command output, not committed to documentation.

## Local URLs

| Service | URL |
| --- | --- |
| Frontend | `http://localhost:5173` |
| Backend | `http://localhost:8000` |
| Swagger UI | `http://localhost:8000/api/docs/` |
| ReDoc | `http://localhost:8000/api/redoc/` |
| OpenAPI schema | `http://localhost:8000/api/schema/` |
| Django admin | `http://localhost:8000/admin/` |

Use `http://localhost:5173` for frontend testing because backend CORS is
configured for that origin.

## Environment File

The `.env` file is loaded by Docker Compose. Important values include:

```env
POSTGRES_DB=ecommerce_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
CORS_ALLOWED_ORIGINS=http://localhost:5173
FRONTEND_URL=http://localhost:5173
REDIS_URL=redis://redis:6379/0
STRIPE_SECRET_KEY=sk_test_replace_me
STRIPE_PUBLISHABLE_KEY=pk_test_replace_me
STRIPE_WEBHOOK_SECRET=whsec_replace_me
OPENAI_API_KEY=
AWS_STORAGE_BUCKET_NAME=
```

Do not commit real API keys, passwords, tokens, or private service credentials.

## Daily Commands

Run from `ecommerce-infra`:

```bash
docker compose up -d
docker compose ps
docker compose logs -f web
docker compose logs -f frontend
docker compose exec web python manage.py migrate
docker compose exec web python manage.py seed_demo_data
docker compose down
```

Rebuild when dependency files or Dockerfiles change:

```bash
docker compose up --build -d
```

Run backend checks:

```bash
docker compose exec web python manage.py test
docker compose exec web flake8
```

Run frontend checks:

```bash
docker compose exec frontend npm run test
docker compose exec frontend npm run build
```

## Reset Local Data

Use this when local database state is stale or inconsistent:

```bash
docker compose down -v
docker compose up --build -d
docker compose exec web python manage.py migrate
docker compose exec web python manage.py seed_demo_data
```

## Media and Product Images

Product image seed assets live in the backend repository under:

```text
catalog/seed_images/products/
```

During `seed_demo_data`, the backend copies those images into configured media
storage and creates `ProductImage` records. Generated `media/` output is local
runtime data and should not be committed.

If AWS S3 settings are configured in `.env`, media files are stored in S3.
Otherwise, local filesystem storage is used.

## Troubleshooting

If the frontend shows `Failed to fetch`, check service health and backend logs:

```bash
docker compose ps
docker compose logs -f web
```

If API errors mention missing columns, run migrations:

```bash
docker compose exec web python manage.py migrate
```

If product images are missing, reseed demo data:

```bash
docker compose exec web python manage.py seed_demo_data
```

If containers behave unexpectedly after dependency changes, rebuild:

```bash
docker compose up --build -d
```

## Development Notes

- Keep the three repositories as siblings so Docker volume paths resolve correctly.
- Run Docker Compose from this repository.
- Keep `.env` local and out of Git.
- Rebuild containers after dependency changes.
- Reset volumes only when local data can be safely recreated.
