# 🤖 Agent Dev Environment

Docker-based Python development environment for building AI Agents.
Edit on **Windows + VS Code**, run on **Linux Docker**.

---

## Stack

| Component | Version | Purpose |
|-----------|---------|---------|
| Python | 3.13 (latest stable) | Runtime |
| PostgreSQL | 16 | Persistent storage |
| pgAdmin | latest | DB web UI (optional) |

Pre-installed Python packages: LangChain, LangGraph, Anthropic SDK, FastAPI, Gradio, psycopg2, SQLAlchemy, and more (see `docker/requirements.txt`).

---

## Prerequisites (Linux host)

```bash
# Docker Engine
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER   # log out & back in

# Docker Compose v2
docker compose version           # should print v2.x
```

## Prerequisites (Windows — VS Code)

1. Install **Docker Desktop** (with WSL2 backend) OR point VS Code at the Linux host via SSH.
2. Install VS Code extensions:
   - **Remote - SSH** (`ms-vscode-remote.remote-ssh`)
   - **Dev Containers** (`ms-vscode-remote.remote-containers`)

---

## Quick Start

### 1 — Clone / copy this project onto the Linux host

```bash
git clone <your-repo> ~/agent-dev-env
cd ~/agent-dev-env
```

### 2 — Create your `.env` file

```bash
cp .env.example .env
nano .env          # fill in API keys
```

### 3 — Build & start

```bash
docker compose up -d --build
```

### 4 — Open in VS Code (Windows)

**Option A — Dev Containers (recommended)**
1. In VS Code: `Ctrl+Shift+P` → *Dev Containers: Open Folder in Container…*
2. Point to this project folder.
3. VS Code will attach directly into `agent-dev` with all extensions auto-installed.

**Option B — Remote SSH then Dev Containers**
1. `Ctrl+Shift+P` → *Remote-SSH: Connect to Host…* → select your Linux host.
2. Open the project folder on the remote, then *Reopen in Container*.

---

## Common Commands

```bash
# Start all services
docker compose up -d

# Open a shell in the dev container
docker exec -it agent-dev bash

# View logs
docker compose logs -f agent-dev
docker compose logs -f postgres

# Stop everything
docker compose down

# Wipe DB volume and start fresh
docker compose down -v

# Start with pgAdmin UI (http://localhost:5050)
docker compose --profile tools up -d
```

---

## Port Map

| Port | Service |
|------|---------|
| 8000 | FastAPI / uvicorn |
| 7860 | Gradio |
| 8080 | Streamlit |
| 8888 | Jupyter |
| 5432 | PostgreSQL (host access) |
| 5050 | pgAdmin (--profile tools) |

---

## Project Structure

```
agent-dev-env/
├── .devcontainer/
│   └── devcontainer.json     ← VS Code Remote Containers config
├── docker/
│   ├── Dockerfile            ← Python 3.13 dev image
│   └── requirements.txt      ← Python dependencies
├── scripts/
│   └── init-db.sql           ← Postgres schema (runs on first boot)
├── workspace/                ← Your agent code lives here
├── docker-compose.yml
├── .env.example
└── .gitignore
```

---

## Database Access

**From inside the container:**
```bash
psql $DATABASE_URL
```

**From Windows (DBeaver / TablePlus / pgAdmin):**
- Host: `<linux-host-ip>`
- Port: `5432`
- DB: `agentdb`
- User: `agentdev`
- Password: `agentdev`

---

## Adding Python Packages

```bash
# Inside the container
pip install <package>

# Or add to docker/requirements.txt and rebuild
docker compose up -d --build agent-dev
```
