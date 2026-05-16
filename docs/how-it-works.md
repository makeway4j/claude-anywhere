# How It Works

## The Core Idea

code-server runs VS Code as a web application on your server. Your browser connects to it over HTTPS and renders the full VS Code interface. Everything — your files, your terminal, Claude Code — runs on the server. Your device is just a display.

```
┌─────────────────────────────────────────────┐
│              Your Server                     │
│                                             │
│  code-server :8080                          │
│  ├── VS Code UI                             │
│  ├── Terminal (bash)                        │
│  ├── Claude Code                            │
│  └── Your Projects (/opt/*)                 │
└─────────────────────────────────────────────┘
          ↑
    Nginx Proxy Manager
    code.yourdomain.com → :8080
          ↑
    Cloudflare + Router (port 443)
          ↑
    Any browser, anywhere
```

## Why There's No Sync

All devices connect to the same server and see the same files. There's nothing to sync because the files never leave the server. Save on your phone, open on your laptop — it's already there.

## SSL via Cloudflare DNS Challenge

Standard Let's Encrypt requires inbound port 80 to verify domain ownership. Many ISPs block port 80 on residential connections. The Cloudflare DNS challenge bypasses this entirely — Let's Encrypt verifies ownership by checking a DNS TXT record that certbot creates via the Cloudflare API, with no inbound port 80 required.

## Websockets

code-server requires WebSocket support in the proxy. Without it, the VS Code interface loads but the terminal and editor won't function. Nginx Proxy Manager's "Websockets Support" toggle handles this automatically.
