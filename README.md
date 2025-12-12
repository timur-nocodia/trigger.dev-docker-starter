# Trigger.dev Self-Hosting Docker

Self-host Trigger.dev v4 using Docker Compose.

## Requirements

- **Docker** 20.10.0+
- **Docker Compose** 2.20.0+
- **Webapp**: 3+ vCPU, 6+ GB RAM
- **Worker**: 4+ vCPU, 8+ GB RAM (scales with concurrency)

## Quick Start

1. **Copy and configure environment**:
   ```bash
   cp .env.example .env
   ```

2. **Generate secrets** (replace the defaults in `.env`):
   ```bash
   openssl rand -hex 16  # Run 4 times for each secret
   ```

3. **Start all services**:
   ```bash
   docker compose up -d
   ```

4. **Access the dashboard** at http://localhost:8030

5. **Login with Magic Link** - check the webapp logs for the sign-in link:
   ```bash
   docker compose logs -f webapp
   ```

## Services

| Service | Port | Description |
|---------|------|-------------|
| webapp | 8030 | Trigger.dev dashboard & API |
| postgres | 5434 | PostgreSQL database |
| redis | 6389 | Redis cache |
| clickhouse | 9123 (HTTP), 9090 (Native) | ClickHouse analytics |
| electric | - | ElectricSQL sync |
| registry | 5000 | Docker registry for task images |
| minio | 9000 (API), 9001 (Console) | Object storage (S3-compatible) |
| supervisor | - | Worker supervisor |
| docker-proxy | - | Docker socket proxy |

## Configuration

### Environment Variables

Create `.env` from `.env.example` and configure:

- **Secrets**: `SESSION_SECRET`, `MAGIC_LINK_SECRET`, `ENCRYPTION_KEY`, `MANAGED_WORKER_SECRET`
- **Database**: `POSTGRES_PASSWORD`, `DATABASE_URL`
- **URLs**: `APP_ORIGIN`, `LOGIN_ORIGIN`, `API_ORIGIN` (set to your public URL in production)
- **Object Storage**: `OBJECT_STORE_ACCESS_KEY_ID`, `OBJECT_STORE_SECRET_ACCESS_KEY`

### Registry Setup

Default credentials (set in `.env`):
- **URL**: `localhost:5000`
- **Username**: `DOCKER_REGISTRY_USERNAME`
- **Password**: `DOCKER_REGISTRY_PASSWORD`

To change the password:
```bash
htpasswd -Bbn <username> <password> > registry/auth.htpasswd
```

### Object Storage (MinIO)

- **Console**: http://localhost:9001
- **Username**: `MINIO_ROOT_USER` (default: `admin`)
- **Password**: `MINIO_ROOT_PASSWORD`

A bucket named `packets` is created automatically.

## CLI Usage

Configure your Trigger.dev project to use the self-hosted instance:

```bash
# Initialize a new project
npx trigger.dev@latest init -p <project-ref> -a http://localhost:8030

# Or set the environment variable
export TRIGGER_API_URL=http://localhost:8030
```

## Stopping

```bash
docker compose down
```

To remove all data:
```bash
docker compose down -v
```

## Production Notes

1. **Change all default passwords and secrets**
2. **Set `APP_ORIGIN`, `LOGIN_ORIGIN`, `API_ORIGIN`** to your public URL
3. **Configure TLS/SSL** via reverse proxy (nginx, Traefik, etc.)
4. **Set up proper backups** for PostgreSQL and ClickHouse volumes

## Documentation

- [Self-hosting Guide](https://trigger.dev/docs/self-hosting/docker)
- [Environment Variables](https://trigger.dev/docs/self-hosting/env/webapp)
- [Trigger.dev Docs](https://trigger.dev/docs)
