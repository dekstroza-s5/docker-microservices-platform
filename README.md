# Docker Microservices Platform

Local integration environment with an API, PostgreSQL, Redis, RabbitMQ and NGINX. It demonstrates service discovery, health-gated startup, persistent database storage and reverse proxying.

## Architecture

```text
client -> NGINX -> API
                   ├── PostgreSQL
                   ├── Redis
                   └── RabbitMQ
```

Compose DNS resolves services by name. Only NGINX and optional development endpoints should be exposed outside the network.

## Start

```bash
cp .env.example .env
docker compose config
docker compose build --pull
docker compose up -d
docker compose ps
```

Expected health check:

```bash
curl --fail http://localhost:8080/health
# {"status":"ok"}
```

Inspect service behavior:

```bash
docker compose logs -f api nginx
docker compose exec postgres pg_isready -U app
docker compose exec redis redis-cli ping
docker compose exec rabbitmq rabbitmq-diagnostics -q ping
```

## Failure exercise

Stop PostgreSQL and observe dependency behavior:

```bash
docker compose stop postgres
docker compose ps
docker compose logs api
docker compose start postgres
```

The Compose dependency condition protects initial startup; applications still need runtime retries and sensible connection timeouts when dependencies fail later.

## Data lifecycle

PostgreSQL uses a named volume:

```bash
docker volume ls
docker compose down
docker compose up -d
```

Data survives a normal `down`. `docker compose down -v` deliberately deletes it.

## Troubleshooting

- API unavailable: inspect NGINX upstream resolution and API logs;
- database unhealthy: run `pg_isready` and inspect initialization logs;
- port conflict: identify the local process using 8080;
- stale image: rebuild with `--no-cache`;
- dependency remains unhealthy: inspect the exact healthcheck command.

The bundled credentials are for an isolated local lab. Real deployments must use generated secrets, restricted networks, backups and service-specific monitoring.
