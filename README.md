# Effective Mobile — DevOps Test Assignment

Simple web application served through nginx as a reverse proxy, running in Docker containers.

## Technologies

| Layer | Technology |
|-------|-----------|
| Backend | Python 3.12 (http.server) |
| Reverse proxy | nginx 1.27 (Alpine) |
| Orchestration | Docker Compose v3.9 |

## Project Structure

```
.
├── backend/
│   ├── Dockerfile   # Python app image
│   └── app.py       # HTTP server on port 8080
├── nginx/
│   └── nginx.conf   # Reverse proxy configuration
├── docker-compose.yml
├── .env             # Optional environment overrides
├── .gitignore
└── README.md
```

## Quick Start

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) ≥ 24
- [Docker Compose](https://docs.docker.com/compose/) ≥ 2

### Run

```bash
git clone https://github.com/josovd2/effective-mobile.git
cd effective-mobile

docker compose up --build -d
```

### Verify

```bash
curl http://localhost
# Expected: Hello from Effective Mobile!
```

### Stop

```bash
docker compose down
```

## Architecture

```
  ┌─────────┐         ┌──────────────────────────────┐
  │  Client │ ──80──► │  nginx (em-nginx)             │
  └─────────┘         │  port 80 exposed to host      │
                       │                              │
                       │  proxy_pass http://backend:8080
                       └──────────────┬───────────────┘
                                      │ app-net (bridge)
                       ┌──────────────▼───────────────┐
                       │  backend (em-backend)         │
                       │  Python http.server :8080     │
                       │  NOT exposed to host          │
                       └──────────────────────────────┘
```

**Request flow:**
1. Client sends `GET /` to `localhost:80`
2. nginx receives the request and forwards it to `backend:8080` inside the `app-net` Docker network
3. Python server responds with `Hello from Effective Mobile!`
4. nginx returns the response to the client

## Security Notes

- Backend port **8080 is not published** to the host — it is reachable only within the internal Docker network `app-net`
- Backend container runs as a **non-root user** (`appuser`)
- nginx version string is hidden (`server_tokens off`)
- No secrets are stored in the repository; sensitive values can be placed in `.env` (already in `.gitignore` for `.env.local`)

## Configuration

You can override the host port nginx binds to by editing `.env`:

```env
NGINX_HOST_PORT=8080   # bind nginx to 8080 on the host instead of 80
```
