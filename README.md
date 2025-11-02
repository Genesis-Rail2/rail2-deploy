# rail2-deploy
# Rail-2 Deploy

Boring-Swiss deployment repo for Genesis Rail-2.  
Ships pre-built containers from GitHub Container Registry (GHCR) to a self-hosted runner via Docker Compose.

## What this repo does
- Pulls images from `ghcr.io/<org>/<repo>:main`
- Starts/updates containers with `docker compose up -d`
- Runs entirely from a GitHub Actions workflow (`.github/workflows/deploy.yml`)

## Prerequisites
- Organization secrets: `GHCR_USERNAME`, `GHCR_TOKEN`
- One self-hosted runner (Ubuntu) with Docker:
  ```bash
  curl -fsSL https://get.docker.com | sh
  sudo usermod -aG docker $USER && newgrp docker
Runner registered in this repo: Settings → Actions → Runners → New self-hosted runner

Quick start
Ensure each service repo has a publish.yml pushing to GHCR.

Edit docker-compose.yml, add services/images as needed.

In Actions → deploy → Run workflow (or push to main).

Verify on runner:

bash
Copy code
docker ps
docker compose logs -f sac
Configuration
Non-sensitive defaults are written to .env by the workflow.

Add real secrets later via Docker secrets or a vault (not committed).

Common commands (runner host)
bash
Copy code
# update to latest images
docker compose pull && docker compose up -d

# live logs (Ctrl-C to exit)
docker compose logs -f <service>

# restart one service
docker compose restart <service>

# stop all
docker compose down
Structure
bash
Copy code
.
├─ docker-compose.yml        # services/image tags
└─ .github/workflows/
   └─ deploy.yml             # GHCR login, pull, compose up -d
Troubleshooting
“permission denied”: ensure user is in docker group, re-login or newgrp docker.

Image not found: confirm the service repo published to ghcr.io/<org>/<repo>:main.

Runner offline: repo → Settings → Actions → Runners → check status; restart service:

bash
Copy code
cd ~/actions-runner && sudo ./svc.sh restart
