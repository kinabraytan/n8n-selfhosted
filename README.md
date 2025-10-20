# n8n on Docker Desktop Stack – Foolproof Guide

## What is this?
This project lets you run **[n8n](https://n8n.io/)** (an open-source workflow automation tool) locally or remotely, with persistent data and secure access via Cloudflare Tunnel. It uses Docker Compose to orchestrate n8n, PostgreSQL, and Cloudflared. You can expose n8n using your own domain for webhooks and integrations.

---

## Quick Start – Step by Step

### 1. Prerequisites
- **Docker Desktop** ([Download](https://www.docker.com/products/docker-desktop/))
- **Docker Compose** (included with Docker Desktop)
- **Cloudflared** ([Download & Install Guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/))
- **Cloudflare Account** ([Sign Up](https://dash.cloudflare.com/sign-up))
- **Your own domain** (e.g., `yourdomain.com`) registered with Cloudflare

### 2. Clone or Copy This Repository
```
git clone https://github.com/kinabraytan/n8n-selfhosted.git
cd <project-folder>
```

### 3. Create `.env` File
Create a file named `.env` in the project root. Example:
```
POSTGRES_USER=your_db_user
POSTGRES_PASSWORD=your_db_password
POSTGRES_DB=your_db_name
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=postgres
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=your_db_name
DB_POSTGRESDB_USER=your_db_user
DB_POSTGRESDB_PASSWORD=your_db_password
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=your_n8n_user
N8N_BASIC_AUTH_PASSWORD=your_n8n_password
N8N_ENCRYPTION_KEY=your_encryption_key
WEBHOOK_URL=https://yourdomain.com/webhook/
CLOUDFLARE_TUNNEL_TOKEN=your_cloudflare_tunnel_token
```

### 4. Install Cloudflared & Set Up Tunnel
- Download Cloudflared for your OS: [Cloudflared Downloads](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)
- Authenticate Cloudflared:
  - Run: `cloudflared login`
  - Follow prompts to connect to your Cloudflare account
- Create a tunnel:
  - Run: `cloudflared tunnel create n8n-tunnel`
- Configure the tunnel:
  - Edit `cloudflared/config.yml` (see example in this repo)
  - Add your domain to Cloudflare and set up a CNAME for your tunnel
- Get your tunnel token:
  - Run: `cloudflared tunnel token <tunnel-name>`
  - Paste the token in your `.env` as `CLOUDFLARE_TUNNEL_TOKEN`
- Place `config.yml` and `cert.pem` in the `cloudflared/` folder

**Official Cloudflared Setup Docs:**
- [Cloudflared Tunnel Setup](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/create-tunnel/)
- [Expose Local Service](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/routing-to-tunnel/)

### 5. Start the Stack
```
docker-compose up -d
```

### 6. Access n8n
- Locally: [http://localhost:5678](http://localhost:5678)
- Remotely: `https://yourdomain.com` (via Cloudflare Tunnel)

### 7. Stop the Stack
```
docker-compose down
```

### 8. Logs & Troubleshooting
- View logs: `docker-compose logs -f n8n` (or `postgres`, `cloudflared`)
- If Cloudflared fails, check your token, config, and Cloudflare dashboard
- For webhook functionality, make sure your domain is set up and `WEBHOOK_URL` is correct

---

## Using Your Own Domain
- Register your domain with Cloudflare
- Set up a CNAME record pointing to your Cloudflare Tunnel
- Update `WEBHOOK_URL` and related variables in `.env`
- See [Cloudflare Custom Domains Guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/routing-to-tunnel/dns/)

## Alternative Exposure Options
- For local-only use, you can skip Cloudflared and access n8n at `localhost:5678`
- For public access, Cloudflared is recommended for security and reliability
- You may also use [ngrok](https://ngrok.com/) or similar tools, but Cloudflared is preferred for custom domains and SSL

---

## Key Files
- `docker-compose.yml`: Main stack definition
- `cloudflared/config.yml`: Tunnel configuration
- `cloudflared/cert.pem`: Cloudflare certificate
- `credentials.json`: n8n encrypted API credentials
- `.env`: Required for secrets and config

## Official Documentation
- [n8n Docker Hosting](https://docs.n8n.io/hosting/docker/)
- [Cloudflared Tunnels](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Docker Compose](https://docs.docker.com/compose/)

---

## FAQ & Tips
- **Cloudflared won’t connect?** Double-check your token, config, and Cloudflare dashboard.
- **Webhooks not working?** Make sure your domain is set up and `WEBHOOK_URL` matches your tunnel URL.
- **Forgot your tunnel token?** Run `cloudflared tunnel token <tunnel-name>` again.
- **Need to restore data?** Use `n8n_backup.sql` for Postgres dumps.

---

## Credits
Created for n8n workflow automation with secure, persistent, and remote access using Docker and Cloudflare Tunnel.

---

If you get stuck, check the official docs above or ask for help in the n8n or Cloudflare communities!
