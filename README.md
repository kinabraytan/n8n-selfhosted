<p align="center">
</p>


<p align="center">
  <a href="https://www.docker.com/products/docker-desktop/"><img src="https://img.shields.io/badge/Docker-Desktop-blue?logo=docker" alt="Docker Desktop"/></a>
  <a href="https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/"><img src="https://img.shields.io/badge/Cloudflare-Tunnel-orange?logo=cloudflare" alt="Cloudflare Tunnel"/></a>
  <a href="https://github.com/kinabraytan/n8n-selfhosted/blob/main/LICENSE"><img src="https://img.shields.io/github/license/kinabraytan/n8n-selfhosted" alt="License"/></a>
  <a href="https://github.com/kinabraytan/n8n-selfhosted/actions"><img src="https://github.com/kinabraytan/n8n-selfhosted/workflows/CI/badge.svg" alt="GitHub Actions"/></a>
</p>


## 📑 Table of Contents


- [n8n on Docker Desktop Stack – Foolproof Guide](#n8n-on-docker-desktop-stack--foolproof-guide)
  - [🚀 What is this?](#-what-is-this)
  - [⚡ Quick Start – Step by Step](#-quick-start--step-by-step)
    - [1. Prerequisites 🛠️](#1-prerequisites-️)
    - [2. Clone or Copy This Repository 📦](#2-clone-or-copy-this-repository-)
    - [3. Create `.env` File 🔑](#3-create-env-file-)
    - [4. Install Cloudflared \& Set Up Tunnel ☁️🔒](#4-install-cloudflared--set-up-tunnel-️)
    - [5. Start the Stack ▶️](#5-start-the-stack-️)
    - [6. Access n8n 🌍](#6-access-n8n-)
    - [7. Stop the Stack ⏹️](#7-stop-the-stack-️)
    - [8. Logs \& Troubleshooting 🪛](#8-logs--troubleshooting-)
  - [🌐 Using Your Own Domain](#-using-your-own-domain)
  - [🔒 Alternative Exposure Options](#-alternative-exposure-options)
  - [📁 Key Files](#-key-files)
  - [📚 Official Documentation](#-official-documentation)
  - [💡 FAQ \& Tips](#-faq--tips)
  - [🙌 Credits](#-credits)


<p align="center">

  <img src="https://docs.n8n.io/assets/img/workflow-example.png" alt="n8n workflow example" width="600"/>
</p>

# n8n on Docker Desktop Stack – Foolproof Guide


## 🚀 What is this?

This project lets you run **[n8n](https://n8n.io/)** (an open-source workflow automation tool) locally or remotely, with persistent data and secure access via Cloudflare Tunnel. It uses Docker Compose to orchestrate n8n, PostgreSQL, and Cloudflared. You can expose n8n using your own domain for webhooks and integrations.



## ⚡ Quick Start – Step by Step
### 1. Prerequisites 🛠️
### 2. Clone or Copy This Repository 📦
```bash

git clone https://github.com/kinabraytan/n8n-selfhosted.git
cd <project-folder>
```
### 3. Create `.env` File 🔑
Create a file named `.env` in the project root. Example:
```env
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

### 4. Install Cloudflared & Set Up Tunnel ☁️🔒
- 📥 Download Cloudflared for your OS: [Cloudflared Downloads](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)
- 🔑 Authenticate Cloudflared:
  - Run: `cloudflared login`
  - Follow prompts to connect to your Cloudflare account
- 🚇 Create a tunnel:
  - Run: `cloudflared tunnel create n8n-tunnel`
- ⚙️ Configure the tunnel:
  - Edit `cloudflared/config.yml` (see example in this repo)
  - Add your domain to Cloudflare and set up a CNAME for your tunnel
- 🪪 Get your tunnel token:
  - Run: `cloudflared tunnel token <tunnel-name>`
  - Paste the token in your `.env` as `CLOUDFLARE_TUNNEL_TOKEN`
- 📂 Place `config.yml` and `cert.pem` in the `cloudflared/` folder

**Official Cloudflared Setup Docs:**

### 5. Start the Stack ▶️
```bash
docker-compose up -d
```

### 6. Access n8n 🌍

### 7. Stop the Stack ⏹️
```bash
docker-compose down
```

### 8. Logs & Troubleshooting 🪛
- 📜 View logs: `docker-compose logs -f n8n` (or `postgres`, `cloudflared`)
- ❌ If Cloudflared fails, check your token, config, and Cloudflare dashboard
- 🔗 For webhook functionality, make sure your domain is set up and `WEBHOOK_URL` is correct

---

---

## 🌐 Using Your Own Domain
- Register your domain with Cloudflare
- Set up a CNAME record pointing to your Cloudflare Tunnel
- Update `WEBHOOK_URL` and related variables in `.env`
- See [Cloudflare Custom Domains Guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/routing-to-tunnel/dns/)

---

## 🔒 Alternative Exposure Options
- For local-only use, you can skip Cloudflared and access n8n at `localhost:5678`
- For public access, Cloudflared is recommended for security and reliability
- You may also use [ngrok](https://ngrok.com/) or similar tools, but Cloudflared is preferred for custom domains and SSL

---

---

## 📁 Key Files
- `docker-compose.yml`: Main stack definition
- `cloudflared/config.yml`: Tunnel configuration
- `cloudflared/cert.pem`: Cloudflare certificate
- `credentials.json`: n8n encrypted API credentials
- `.env`: Required for secrets and config

---

## 📚 Official Documentation
- [n8n Docker Hosting](https://docs.n8n.io/hosting/docker/)
- [Cloudflared Tunnels](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Docker Compose](https://docs.docker.com/compose/)

---

---

## 💡 FAQ & Tips
- **Cloudflared won’t connect?** Double-check your token, config, and Cloudflare dashboard.
- **Webhooks not working?** Make sure your domain is set up and `WEBHOOK_URL` matches your tunnel URL.
- **Forgot your tunnel token?** Run `cloudflared tunnel token <tunnel-name>` again.
- **Need to restore data?** Use `n8n_backup.sql` for Postgres dumps.

---

---

## 🙌 Credits
Created for n8n workflow automation with secure, persistent, and remote access using Docker and Cloudflare Tunnel.


<p align="center">
  <a href="https://github.com/kinabraytan" title="kinabraytan">
    <img src="https://avatars.githubusercontent.com/u/15175359?v=4" width="60" alt="kinabraytan" style="border-radius:50%;"/>
  </a>
</p>


<p align="center">
  <b>Helpful Links</b><br>
  <a href="https://docs.n8n.io/hosting/docker/">n8n Docker Docs</a> •
  <a href="https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/">Cloudflared Docs</a> •
  <a href="https://docs.docker.com/compose/">Docker Compose Docs</a> •
  <a href="https://community.n8n.io/">n8n Community</a> •
  <a href="https://community.cloudflare.com/">Cloudflare Community</a>
</p>

If you get stuck, check the official docs above or ask for help in the n8n or Cloudflare communities!
