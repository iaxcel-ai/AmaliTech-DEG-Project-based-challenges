# Kora Analytics API — DevOps Implementation

A containerised Node.js API with a fully automated CI/CD pipeline deployed to Render.

**Live URL:** https://amalitech-deg-project-based-challenges-gycg.onrender.com

---

## Architecture Overview

```
GitHub (main branch)
       │
       ▼
GitHub Actions CI/CD Pipeline
  ├── 1. Test    — Jest test suite must pass
  ├── 2. Build   — Docker image built and pushed to GHCR
  └── 3. Deploy  — Render deploy hook triggered
       │
       ▼
Render Web Service (Docker)
  └── kora-analytics-api container (port 3000)
```

---

## API Endpoints

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/health` | Returns `{"status":"ok"}` |
| GET | `/metrics` | Returns uptime and memory usage |
| POST | `/data` | Accepts a JSON body and echoes it back |

---

## Running Locally

### With Docker Compose

```bash
cp .env.example .env
docker compose up --build
```

The API will be available at `http://localhost:3000`.

### Without Docker

```bash
cd app
npm install
npm start
```

---

## Running Tests

```bash
cd app
npm test
```

---

## Project Structure

```
dev-ops/DeployReady/
├── app/                  # Node.js application source
│   ├── index.js
│   ├── index.test.js
│   └── package.json
├── Dockerfile            # Multi-stage Docker build, non-root user
├── docker-compose.yml    # Local development setup
├── .env.example          # Environment variable template
├── .dockerignore
└── DEPLOYMENT.md         # Cloud deployment documentation
```

---

## CI/CD Pipeline

Defined in `.github/workflows/deploy.yml`. Triggers on every push to `main`.

**Required GitHub secrets:**
- `RENDER_DEPLOY_HOOK` — Deploy hook URL from Render service settings

See [DEPLOYMENT.md](./DEPLOYMENT.md) for full deployment details.
