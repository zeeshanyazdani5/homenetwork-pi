# homenetwork-pi
Docker image for my RPI running various network tools. Managed by BalenaOS.

## Tailscale

This repo runs Tailscale as a separate Balena service. The container stores its
node state in the `tailscale` volume, uses the host network, and exposes the
Pi to your private Tailscale network without opening router ports.

You need a Tailscale account. Create one at https://tailscale.com, then create
an auth key in the Tailscale admin console and add it to the Balena fleet or
device environment variables:

```sh
TS_AUTHKEY=tskey-auth-...
```

For normal remote access to the Pi, install Tailscale on your phone/laptop and
sign in to the same account. After the Balena device comes online, connect to
`homenetwork-pi` using its Tailscale IP or MagicDNS name.

To reach other devices on your home LAN while away, use the Pi as a Tailscale
subnet router:

1. Find your home subnet, usually something like `192.168.1.0/24` or
   `10.0.0.0/24`.
2. Add this Balena environment variable:

   ```sh
   TS_ROUTES=192.168.1.0/24
   ```

3. In the Tailscale admin console, approve the advertised subnet route for
   `homenetwork-pi`.
4. Make sure the remote phone/laptop has Tailscale connected. It should then
   be able to reach LAN devices by their normal home IP addresses.

If you want Pi-hole DNS while away from home, set the Pi's Tailscale IP as a
nameserver in the Tailscale admin console DNS settings.