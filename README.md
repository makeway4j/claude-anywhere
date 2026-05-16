# claude-anywhere

> Access Claude Code and your full development environment from any device — phone, tablet, laptop, or desktop — through a browser.
<img width="1450" height="1012" alt="image" src="https://github.com/user-attachments/assets/812942a7-eb15-47e0-8eee-edee8ccba23e" />

![code-server](https://img.shields.io/badge/code--server-v4.118.0-blue) ![Let's Encrypt](https://img.shields.io/badge/SSL-Let's%20Encrypt-green) ![Claude Code](https://img.shields.io/badge/Claude%20Code-v2.1.143-orange)

---

## What Is This?

**claude-anywhere** is a self-hosted setup that runs VS Code and Claude Code on your homelab server, accessible from any browser via a secure public URL. No local install needed on any device — just open your browser and code.

```
Phone / Laptop / Desktop (browser)
         ↓
  https://code.yourdomain.com
         ↓
  Nginx Proxy Manager (SSL)
         ↓
  code-server + Claude Code
  (running on your homelab)
```

---

## Features

- ✅ Full VS Code in any browser
- ✅ Claude Code accessible from terminal inside the browser
- ✅ All devices see the same files — no sync needed
- ✅ HTTPS via Let's Encrypt (Cloudflare DNS challenge)
- ✅ Works on mobile (add to home screen for app-like experience)
- ✅ Self-hosted — your code never leaves your server

---

## Stack

| Component | Role |
|---|---|
| [code-server](https://github.com/coder/code-server) | VS Code in the browser |
| [Claude Code](https://claude.ai/code) | AI coding assistant in the terminal |
| [Nginx Proxy Manager](https://nginxproxymanager.com/) | Reverse proxy + SSL |
| [Cloudflare](https://cloudflare.com) | DNS + SSL challenge |
| Proxmox LXC | Homelab container hosting |

---

## Requirements

- A Linux server or LXC container (Ubuntu 22.04/24.04)
- A domain name with DNS on Cloudflare
- Nginx Proxy Manager running on your network
- Ports 80 and 443 forwarded to NPM on your router
- An Anthropic account for Claude Code

---

## Quick Start

### 1. Install code-server

```bash
curl -fsSL https://code-server.dev/install.sh | sh
systemctl enable --now code-server@root
```

### 2. Configure code-server

Edit `/root/.config/code-server/config.yaml`:

```yaml
bind-addr: 0.0.0.0:8080
auth: password
password: YOUR_STRONG_PASSWORD
cert: false
```

Restart:
```bash
systemctl restart code-server@root
```

### 3. Install Claude Code

```bash
curl -fsSL https://claude.ai/install.sh | bash
source ~/.bashrc
claude --version
```

### 4. Configure Nginx Proxy Manager

Add a new Proxy Host:

| Field | Value |
|---|---|
| Domain | `code.yourdomain.com` |
| Scheme | `http` |
| Forward Hostname | `YOUR_SERVER_IP` |
| Forward Port | `8080` |
| Websockets Support | ✅ ON |
| SSL Certificate | Request Let's Encrypt |
| Force HTTPS | ✅ ON |
| DNS Challenge | Cloudflare (recommended) |

> **Note:** If your ISP blocks inbound port 80 (common on residential connections), use the Cloudflare DNS challenge for SSL — it bypasses the HTTP verification entirely.

### 5. Cloudflare DNS

Add an A record:

| Type | Name | Content | Proxy |
|---|---|---|---|
| A | code | YOUR_PUBLIC_IP | Proxied ✅ |

### 6. Authenticate Claude Code

Open `https://code.yourdomain.com`, open the terminal (`Ctrl+backtick`), and run:

```bash
claude
```

Follow the OAuth link to authenticate with your Anthropic account.

---

## Usage

**From any device:**
1. Open browser → `https://code.yourdomain.com`
2. Enter your password
3. Open terminal → `cd /your/project && claude`

**Autonomous Claude Code:**
```bash
claude --dangerously-skip-permissions
```

**Workspace file** (open all projects at once):

```json
{
  "folders": [
    { "name": "Project 1", "path": "/opt/project1" },
    { "name": "Project 2", "path": "/opt/project2" }
  ]
}
```

Save as `~/homelab.code-workspace` → File → Open Workspace from File.

---

## Tips

- **Mobile:** Add `code.yourdomain.com` to your phone's home screen for an app-like experience
- **Long tasks:** Don't close the terminal tab while Claude Code is running — the session stays alive on the server
- **Multi-device:** Don't edit the same file on two devices simultaneously
- **Extensions:** Install once, available everywhere — extensions live on the server

---

## Docs

- [Full Setup Guide](docs/setup.md)
- [How It Works](docs/how-it-works.md)
- [Troubleshooting](docs/troubleshooting.md)

---

## License

MIT
