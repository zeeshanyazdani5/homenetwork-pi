# homenetwork

Homelab network stack. Historically a **BalenaOS + Pi-hole** image for a Raspberry Pi.
Target platform is an **HP Z2 Mini G4** running **Proxmox VE**, with this stack in a
small Debian Docker VM.

## Active stack (Proxmox)

**AdGuard Home + Unbound + Tailscale** — see [`stacks/network/`](stacks/network/).

| Service | Job |
| --- | --- |
| AdGuard Home | Network DNS filtering, UI, local rewrites |
| Unbound | Recursive resolver (privacy) |
| Tailscale | Mesh VPN + optional LAN subnet router |

Migration runbook: [`docs/MIGRATION.md`](docs/MIGRATION.md)  
Phone checklist: [`docs/CHECKLIST.md`](docs/CHECKLIST.md)

## Legacy (Raspberry Pi / Balena)

Root [`docker-compose.yml`](docker-compose.yml), [`balena.yml`](balena.yml), and
[`pihole/`](pihole/) remain until the Pi is cut over. Do not deploy them on the Z2.

### Tailscale (legacy Balena notes)

On Balena, set:

```sh
TS_AUTHKEY=tskey-auth-...
TS_ROUTES=192.168.1.0/24
```

Approve the advertised subnet route in the Tailscale admin console. For the new
stack, the same variables live in `stacks/network/.env` (see `.env.example`).

## Future guests (same Proxmox host)

- [`stacks/media/`](stacks/media/) — Plex + Intel Quick Sync
- [`stacks/apps/`](stacks/apps/) — trading bot and other apps
