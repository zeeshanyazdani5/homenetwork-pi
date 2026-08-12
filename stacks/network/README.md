# Network stack (Proxmox / plain Docker)

**AdGuard Home** (filtering + UI) → **Unbound** (recursive DNS) → root servers  
**Tailscale** for remote access / subnet routing.

This replaces the Balena Pi-hole stack for the Z2 Mini. Keep the Pi on the root compose until cutover.

## Why these tools

| Role | Choice | Why |
| --- | --- | --- |
| Filter / UI | AdGuard Home | Better Docker story than Balena Pi-hole; native DoH/DoT; strong UI; per-client rules |
| Recursive DNS | Unbound | Same privacy goal as before — no Cloudflare/Google seeing every query |
| Remote access | Tailscale | Already in this repo; unchanged role |

Pi-hole is fine on x86; AdGuard Home is the upgrade for a clean Proxmox rebuild (fewer custom images, richer DNS features).

## Quick start (on the network VM)

```sh
sudo mkdir -p /srv/network/data/adguard/{work,conf} /srv/network/data/tailscale
cp .env.example .env
# edit .env

# Seed conf so AGH can start (compose also bind-mounts the template read-only;
# if AGH refuses a read-only conf on first boot, copy instead of bind-mount):
sudo cp adguard/AdGuardHome.yaml /srv/network/data/adguard/conf/

docker compose pull
docker compose up -d
```

1. Open `http://<vm-ip>:3000` — create admin user (first run).
2. Confirm **Settings → DNS → Upstream** is `unbound:5053` (seeded).
3. Point a test client DNS at `<vm-ip>`; `dig @<vm-ip> example.com`.
4. Admin UI afterward: `http://<vm-ip>/` (port 80).

## systemd-resolved

If Docker cannot bind port 53:

```sh
sudo sed -i 's/#DNSStubListener=yes/DNSStubListener=no/' /etc/systemd/resolved.conf
sudo systemctl restart systemd-resolved
```

## Backup

```sh
sudo tar -C /srv/network/data -czf ~/network-data-$(date +%F).tgz .
```

Export AdGuard config from the UI as well (Settings → General → Configuration export).
