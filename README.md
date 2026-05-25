# Distributed Ecommerce Platform

This repository contains the local infrastructure for the distributed ecommerce
project. It runs the frontend, backend, database, cache, and background workers
together with Docker Compose so the full system can be started from one place.

## Project Overview

The application is split into three repositories that should be cloned into the
same parent folder:

- `ecommerce-frontend`: Vue 3 single-page application for customers, vendors,
  checkout, account flows, and AI chat.
- `ecommerce-backend`: Django REST API for authentication, catalog, cart,
  checkout, orders, vendors, tenants, AI, notifications, and background jobs.
- `ecommerce-infra`: Docker Compose setup, environment configuration, and
  project-level documentation.

The frontend talks to the backend over HTTP using `/api/v1/*` endpoints. The
backend stores durable data in PostgreSQL, uses Redis for cache and async task
coordination, and runs Celery workers for background jobs such as emails,
exports, and scheduled AI reports.

For a deeper architecture description, see [ARCHITECTURE.md](ARCHITECTURE.md).

## Tech Stack

| Area | Technology |
| --- | --- |
| Frontend | Vue 3, Vite, Pinia, Vue Router, Tailwind CSS |
| Backend | Python, Django, Django REST Framework |
| Database | PostgreSQL 15 |
| Cache and broker | Redis 7 |
| Background jobs | Celery worker and Celery beat |
| Payments | Stripe integration |
| API docs | drf-spectacular Swagger and ReDoc |
| Containers | Docker and Docker Compose |

## Services

| Service | Purpose | Local URL or port |
| --- | --- | --- |
| `frontend` | Vue/Vite development server | `http://localhost:5173` |
| `web` | Django API server | `http://localhost:8000` |
| `db` | PostgreSQL database | host `5433`, container `5432` |
| `redis` | Redis cache, Celery broker, Celery results | `6379` |
| `celery_worker` | Runs async jobs | internal |
| `celery_beat` | Runs scheduled jobs | internal |

## Documentation
- [Architecture](ARCHITECTURE.md)

## Requirements

- Docker Desktop
- Git

## First Time Setup

1. Clone all three repositories into the same parent folder:

   ```bash
   git clone https://github.com/bashkimhashani/ecommerce-infra
   git clone https://github.com/bashkimhashani/ecommerce-frontend
   git clone https://github.com/bashkimhashani/ecommerce-backend
   ```

2. Move into the infra repository:

   ```bash
   cd ecommerce-infra
   ```

3. Copy the example environment file and fill in any required local values:

   ```bash
   cp .env.example .env
   ```

4. Build and start all services:

   ```bash
   docker compose up --build -d
   ```

5. Apply database migrations:

   ```bash
   docker compose exec web python manage.py migrate
   ```

6. Seed demo tenant, users, catalog, inventory, cart, order, and image data:

   ```bash
   docker compose exec web python manage.py seed_demo_data
   ```

7. Create a Django superuser if you need admin access:

   ```bash
   docker compose exec web python manage.py createsuperuser
   ```

## Demo Accounts

After running `seed_demo_data`, demo users are created for local testing. The
seed command prints the available emails and shared demo password in the
terminal output.

## Daily Usage

Start the stack:

```bash
docker compose up -d
```

Stop the stack:

```bash
docker compose down
```

View backend logs:

```bash
docker compose logs -f web
```

Apply new migrations after pulling backend changes:

```bash
docker compose exec web python manage.py migrate
```

Refresh demo database data:

```bash
docker compose exec web python manage.py seed_demo_data
```

Rebuild after dependency changes:

```bash
docker compose up --build -d
```

## Local URLs

- Frontend: `http://localhost:5173`
- Backend API: `http://localhost:8000/api`
- Swagger: `http://localhost:8000/api/docs`
- ReDoc: `http://localhost:8000/api/redoc`
- Django admin: `http://localhost:8000/admin`

## Common Tasks

Run backend tests:

```bash
docker compose exec web python -m pytest
```

Open a Django shell:

```bash
docker compose exec web python manage.py shell
```

Open a database shell:

```bash
docker compose exec db psql -U "$POSTGRES_USER" -d "$POSTGRES_DB"
```

Restart one service:

```bash
docker compose restart web
```

## Troubleshooting

If products load but images do not appear, refresh the demo data:

```bash
docker compose exec web python manage.py seed_demo_data
```

If an API endpoint fails after pulling new backend code, apply migrations:

```bash
docker compose exec web python manage.py migrate
```

If containers behave unexpectedly after dependency changes, rebuild:

```bash
docker compose up --build -d
```

If you need to reset local containers and volumes, stop the stack and remove
volumes:

```bash
docker compose down -v
```

After removing volumes, run migrations and seed data again.

