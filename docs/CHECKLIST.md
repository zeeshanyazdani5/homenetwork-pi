# Cutover checklist (phone / laptop)

Details: [MIGRATION.md](./MIGRATION.md). Stack: **AdGuard Home + Unbound + Tailscale**.

## Before touching DNS

- [ ] Pi LAN IP: `________________`
- [ ] Router admin URL working
- [ ] Confirm Pi is DNS-only (not LAN DHCP server)
- [ ] Save Pi-hole adlist URLs + local DNS/CNAME notes (AGH has no teleporter import)
- [ ] Tailscale auth key ready; note `TS_ROUTES` (e.g. `192.168.1.0/24`)
- [ ] Proxmox on Z2; SSH OK; host IP ≠ Pi IP
- [ ] Debian 12 VM `network` (2 vCPU / 2 GB / 16+ GB) on `vmbr0`
- [ ] Docker + Compose installed; repo cloned at `stacks/network`
- [ ] `.env` filled; data dirs created; `AdGuardHome.yaml` seeded into conf
- [ ] `docker compose up -d` healthy
- [ ] AGH wizard done (`:3000`); upstream = `unbound:5053`
- [ ] Blocklists / rewrites recreated
- [ ] Test client: manual DNS = temp VM IP — resolve + block + UI
- [ ] Tailscale online; subnet route approved; hostname not `homenetwork-pi`
- [ ] Proxmox snapshot `network-pre-cutover`

## Cutover (reuse Pi IP)

- [ ] Remove Pi DHCP reservation
- [ ] Stop / power off Pi
- [ ] Set network VM to old Pi IP
- [ ] `dig @<ip> example.com` OK; AGH query log shows clients
- [ ] Toggle phone Wi‑Fi; browse; spot-check ads blocked
- [ ] Cellular + Tailscale: reach LAN + AGH UI

## Cutover (new IP)

- [ ] Router DHCP DNS → network VM IP
- [ ] Leave Pi up 24h for rollback
- [ ] After leases look good, power off Pi

## Rollback

- [ ] Multi-device DNS failure → Pi back / router DNS back to Pi
- [ ] Config mistake only → restore snapshot `network-pre-cutover`

## After a quiet day

- [ ] Remove old Tailscale node `homenetwork-pi`
- [ ] Keep Pi SD cold ~1 week; retire Balena fleet when ready
- [ ] Password-manager note: Proxmox, VM id, `.env` path, AGH admin
- [ ] Next guests: `media` (Plex + iGPU), `apps` (trading bot)
