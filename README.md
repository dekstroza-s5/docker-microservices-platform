# Docker Microservices Platform

Local integration environment with an API, worker, PostgreSQL, Redis, RabbitMQ and NGINX.

```bash
cp .env.example .env
docker compose up --build -d
curl http://localhost:8080/health
docker compose ps
```

Services use health checks and dependency conditions. Credentials in the example file are for local development only.
