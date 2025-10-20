# Copilot Instructions for n8n Docker Automation Stack

## Overview
This project orchestrates an n8n automation stack using Docker Compose, with PostgreSQL for persistence and Cloudflare Tunnel (cloudflared) for secure remote access. The architecture is designed for local development on Windows, with persistent data volumes and secure credential management.

## Architecture
- **Services:**
  - `n8n`: Main automation service (Node.js-based), exposed on port 5678, configured via environment variables and `.env` file.
  - `postgres`: Database backend, persistent storage mapped to `C:/Users/Jnebr/n8n_postgres`.
  - `cloudflared`: Exposes n8n via Cloudflare Tunnel, using config/cert in `cloudflared/`.
- **Networking:**
  - All services share the custom `n8n-network` (Docker bridge).
  - Healthchecks are defined for all services to ensure readiness and reliability.
- **Volumes:**
  - n8n data: `C:/Users/Jnebr/n8n_data:/home/node/.n8n`
  - Postgres data: `C:/Users/Jnebr/n8n_postgres:/var/lib/postgresql/data`
  - Cloudflared config: `C:/Users/Jnebr/.cloudflared:/etc/.cloudflared`

## Developer Workflows
- **Start stack:**
  - `docker-compose up -d` (ensure `.env` is present with required secrets)
- **Stop stack:**
  - `docker-compose down`
- **Logs:**
  - `docker-compose logs -f n8n` (or `postgres`, `cloudflared`)
- **Backups:**
  - Use `n8n_backup.sql` for Postgres dumps (ensure correct DB user/role)
- **Credentials:**
  - Managed in `credentials.json` (encrypted, do not commit secrets)

## Patterns & Conventions
- **Environment Variables:**
  - All sensitive config is injected via `.env` (not in repo)
  - Example: `N8N_BASIC_AUTH_USER`, `N8N_ENCRYPTION_KEY`, `CLOUDFLARE_TUNNEL_TOKEN`
- **Cloudflared:**
  - Configured via `cloudflared/config.yml` and `cert.pem`
  - Tunnel hostname and service mapping must match n8n public URL
- **Healthchecks:**
  - Use `/healthz` endpoint for n8n, `pg_isready` for Postgres, and `cloudflared tunnel info` for tunnel
- **Windows Paths:**
  - All volume mounts use absolute Windows paths (adjust if porting to Linux)

## Integration Points
- **External APIs:**
  - Credentials for Google, OpenAI, etc. are managed in `credentials.json`
- **Webhooks:**
  - Exposed via Cloudflare Tunnel; ensure `WEBHOOK_URL` is set correctly in `.env`

## Key Files
- `docker-compose.yml`: Main stack definition
- `cloudflared/config.yml`: Tunnel configuration
- `credentials.json`: Encrypted API credentials
- `.env`: Not in repo; required for secrets

## Example: Add a New Service
- Extend `docker-compose.yml` under `services:`
- Add to `n8n-network` and define healthcheck
- Mount volumes using Windows paths

---
For more, see n8n docs: https://docs.n8n.io/hosting/docker/
