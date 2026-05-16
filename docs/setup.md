# Full Setup Guide

This guide walks through the complete setup of claude-anywhere on a Proxmox homelab with Nginx Proxy Manager and Cloudflare DNS.

---

## Prerequisites

- Proxmox LXC container running Ubuntu 22.04 or 24.04
- Nginx Proxy Manager running on your network (CT or VM)
- Domain registered with Cloudflare as DNS provider
- Router with ports 443 forwarded to your NPM instance
- Anthropic account

---

## Step 1 — Install code-server

SSH into your target container:

```bash
curl -fsSL https://code-server.dev/install.sh | sh
systemctl enable --now code-server@root
systemctl status code-server@root
```

Verify it's running on port 8080:
```bash
ss -tlnp | grep 8080
```

---

## Step 2 — Configure code-server

Edit `/root/.config/code-server/config.yaml`:

```yaml
bind-addr: 0.0.0.0:8080
auth: password
password: YOUR_STRONG_PASSWORD
cert: false
```

Restart after editing:
```bash
systemctl restart code-server@root
```

---

## Step 3 — Install Claude Code

**Native installer (preferred):**
```bash
curl -fsSL https://claude.ai/install.sh | bash
source ~/.bashrc
claude --version
```

**Fallback via npm:**
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc
nvm install 22 && nvm use 22
npm install -g @anthropic-ai/claude-code
claude --version
```

---

## Step 4 — Cloudflare API Token

1. Go to `https://dash.cloudflare.com/profile/api-tokens`
2. Click **Create Token** → use **Edit zone DNS** template
3. Set Zone Resources: `Include` → `Specific zone` → `yourdomain.com`
4. Click **Continue to summary** → **Create Token**
5. Copy the token — it only shows once

---

## Step 5 — Nginx Proxy Manager

Add a new Proxy Host:

**Details tab:**
- Domain Names: `code.yourdomain.com`
- Scheme: `http`
- Forward Hostname/IP: `YOUR_CONTAINER_IP`
- Forward Port: `8080`
- Websockets Support: ✅ ON

**SSL tab:**
- SSL Certificate: Request a new SSL Certificate
- Force SSL: ✅ ON
- Use a DNS Challenge: ✅ ON
- DNS Provider: Cloudflare
- Credentials:
  ```
  dns_cloudflare_api_token = YOUR_TOKEN_HERE
  ```
- Email: your email
- I Agree to Let's Encrypt Terms of Service: ✅ ON

Click **Save**.

---

## Step 6 — Cloudflare DNS

Add an A record in Cloudflare:

| Type | Name | Content | Proxy status |
|---|---|---|---|
| A | code | YOUR_PUBLIC_IP | Proxied (orange cloud) |

---

## Step 7 — Authenticate Claude Code

1. Open `https://code.yourdomain.com` in your browser
2. Enter the password from Step 2
3. Open terminal: `Ctrl+backtick`
4. Run `claude` and follow the OAuth link
5. Claude Code is now authenticated and ready

---

## Step 8 — Create Workspace File (Optional)

In the code-server terminal:

```bash
cat > ~/homelab.code-workspace << 'EOF'
{
  "folders": [
    { "name": "Project 1", "path": "/opt/project1" },
    { "name": "Project 2", "path": "/opt/project2" }
  ],
  "settings": {
    "editor.formatOnSave": true,
    "files.autoSave": "afterDelay"
  }
}
EOF
```

Open via **File → Open Workspace from File** → `~/homelab.code-workspace`.
