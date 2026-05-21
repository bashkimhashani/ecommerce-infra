# Ecommerce Infra

## Requirements
- Docker Desktop
- Git

## First time setup

1. Clone all 3 repos into the same parent folder:
   git clone https://github.com/bashkimhashani/ecommerce-infra
   git clone https://github.com/bashkimhashani/ecommerce-frontend
   git clone https://github.com/bashkimhashani/ecommerce-backend

2. Copy the env file and fill in your values:
   cp .env.example .env

3. Build and start everything:
   docker compose up --build

4. In a new terminal run migrations:
   docker compose exec web python manage.py migrate

5. Create a superuser (optional):
   docker compose exec web python manage.py createsuperuser

## Daily usage
- Start:      docker compose up
- Stop:       docker compose down
- Logs:       docker compose logs -f web
- New migrations from a teammate:
              docker compose exec web python manage.py migrate
- Dependency changes (requirements.txt or package.json changed):
              docker compose up --build

## URLs
- Frontend:   http://localhost:5173
- Backend:    http://localhost:8000/api
- Swagger:    http://localhost:8000/api/docs
- Admin:      http://localhost:8000/admin
