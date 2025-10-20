# Self-hosted n8n on Docker Desktop

<p align="center">
  <a href="https://github.com/kinabraytan/n8n-selfhosted" title="GitHub Repo">
    <img src="https://img.shields.io/github/stars/kinabraytan/n8n-selfhosted?style=social" alt="GitHub Stars"/>
  </a>
  <a href="https://github.com/kinabraytan/n8n-selfhosted" title="Fork this repo">
    <img src="https://img.shields.io/github/forks/kinabraytan/n8n-selfhosted?style=social" alt="GitHub Forks"/>
  </a>
  <a href="#license" title="License">
    <img src="https://img.shields.io/github/license/kinabraytan/n8n-selfhosted" alt="License: Apache 2.0"/>
  </a>
</p>

---

<details>
  <summary><b>📄 License</b></summary>

  <pre>
  Apache License 2.0
  See <a href="./LICENSE">LICENSE file</a> for full text.
  </pre>
</details>

---

<p align="center">
  <img src="https://github.com/kinabraytan/n8ncloud/raw/main/assets/n8ncloud-demo.gif" alt="n8ncloud workflow demo" width="600"/>
</p>

---

## 🚀 What is this?

Run **[n8n](https://n8n.io/)** (open-source workflow automation) locally or remotely, with persistent data and secure access via Cloudflare Tunnel. This stack uses Docker Compose to orchestrate n8n, PostgreSQL, and Cloudflared. You can expose n8n using your own domain for webhooks and integrations.

---

## ⚡ Quick Start

### 1. Prerequisites 🛠️

Welcome! If you're new to Docker, Cloudflare, or self-hosting, don't worry—this guide will walk you through every step. Here’s what you need before you start:

- 🐳 **Docker Desktop**
  - What: Lets you run containers (mini virtual computers) on your machine.
  - Why: n8n, Postgres, and Cloudflared all run in containers.
  - How to check: Open a terminal and run `docker --version`. If you see a version number, you’re good!
  - Install: [Download Docker Desktop](https://www.docker.com/products/docker-desktop/) (Windows/macOS). For Linux, see [Docker Engine install](https://docs.docker.com/engine/install/).

- 🧩 **Docker Compose**
  - What: A tool to run multi-container apps with a single command.
  - Why: This project uses `docker-compose.yml` to start everything at once.
  - How to check: Run `docker compose version` (note the space). If you see a version, you’re set! (Included with Docker Desktop.)

- ☁️ **Cloudflare Account**
  - What: Free account for secure tunneling and remote access.
  - Why: Cloudflare Tunnel exposes n8n securely to the internet (for webhooks, integrations, etc).
  - How to check: Try logging in at [Cloudflare Dashboard](https://dash.cloudflare.com/). If you don’t have an account, sign up for free.

- 💻 **Git** (optional, but recommended)
  - What: Version control tool to download (clone) this project.
  - Why: Makes setup and updates easier.
  - How to check: Run `git --version`. If you see a version, you’re set!
  - Install: [Download Git](https://git-scm.com/downloads)
  - If you don’t want to use Git, you can also click “Download ZIP” on the GitHub repo page and extract it.

> **Tip for Windows users:**
> Use the built-in terminal in VS Code, or [Git Bash](https://gitforwindows.org/) for a smoother command-line experience.

### 2. Clone or Copy This Repository 📦
Clone this repository and enter the project folder:
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
Open your browser and go to your Cloudflare Tunnel URL or `localhost:5678` (if local-only).

---

### 7. Stop the Stack ⏹️
```bash
docker-compose down
```

---

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

## 📚 Helpful Links
- [n8n Docs](https://docs.n8n.io/hosting/docker/)
- [Cloudflared Docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [n8n Community](https://community.n8n.io/)
- [Cloudflare Community](https://community.cloudflare.com/)

---

## 📄 License

This project is licensed under the [Apache License 2.0](https://github.com/n8n-io/n8n/blob/master/packages/n8n/LICENSE.md), the same as the official n8n project. See the LICENSE file for details.

---
