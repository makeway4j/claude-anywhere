# Troubleshooting

## Let's Encrypt Certificate Fails

**Symptom:** NPM shows "Internal Error" when requesting SSL cert.

**Cause 1 — Too many failed attempts:**
Let's Encrypt rate limits failed authorizations to 5 per hour per domain. Wait 1 hour and retry.

**Cause 2 — Port 80 blocked by ISP:**
Many residential ISPs block inbound port 80. Use the Cloudflare DNS challenge instead of HTTP challenge. See [setup.md](setup.md) Step 4-5.

**Cause 3 — Cloudflare proxy interfering:**
Set the DNS record to "DNS only" (grey cloud) during cert issuance, then switch back to Proxied after.

---

## code-server Won't Load

**Check it's running:**
```bash
systemctl status code-server@root
```

**Check the port:**
```bash
ss -tlnp | grep 8080
```

**Restart it:**
```bash
systemctl restart code-server@root
```

---

## Terminal Works But Claude Code Doesn't

**Re-authenticate:**
```bash
claude
# Follow the OAuth link
```

**Check it's installed:**
```bash
which claude
claude --version
```

---

## VS Code Loads But Terminal Is Broken

Websockets are not enabled on your NPM proxy host. Edit the proxy host and toggle **Websockets Support** ON, then save.

---

## Can't Access from Outside Home Network

1. Verify port 443 is forwarded to your NPM instance on your router
2. Check Cloudflare DNS record exists for `code.yourdomain.com`
3. Verify NPM proxy host shows "Online" status
4. Test with a port checker: `https://portchecker.co`

---

## NPM Internal Error (Not SSL Related)

Usually means the proxy host was saved without the SSL tab configured. Delete the proxy host and re-add it, filling in both the Details and SSL tabs before hitting Save.
