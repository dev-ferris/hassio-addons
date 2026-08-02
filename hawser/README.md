# Home Assistant Add-on: Hawser

[Hawser](https://github.com/Finsys/hawser) is a lightweight Go agent that lets
[Dockhand](https://github.com/Finsys/dockhand) manage Docker hosts behind
firewalls, NATs, or in cluster setups.

This add-on packages the upstream image
[`ghcr.io/finsys/hawser`](https://github.com/Finsys/hawser/pkgs/container/hawser)
for use as a Home Assistant add-on. The upstream version it is built from is
pinned in [`build.yaml`](build.yaml) and matches the add-on version.

> ⚠️ **Warning** — This add-on hands out access to the Docker socket of your
> Home Assistant host, which is equivalent to root on that machine. Always
> configure a `TOKEN`, and add TLS or use Edge mode whenever the port is
> reachable from a network you do not fully control.

## Documentation

The complete documentation — installation, security notes, all configuration
options, TLS setup and the Watchdog caveats — lives in [DOCS.md](DOCS.md).
That is also the text shown on the add-on's **Documentation** tab in Home
Assistant.

## Quick start

1. Add this repository to Home Assistant: **Settings → Add-ons → Add-on Store
   → ⋮ → Repositories** and paste
   `https://github.com/dev-ferris/hassio-addons`.
2. Install the **Hawser** add-on and disable *Protection mode* on its **Info**
   tab, so the Supervisor mounts the Docker socket.
3. Set `TOKEN` on the **Configuration** tab. For Edge mode, also set
   `DOCKHAND_SERVER_URL` to your Dockhand server's `wss://` URL and clear the
   host port under **Network**.
4. Start the add-on.

## Support

Issues with this add-on should be reported to
<https://github.com/dev-ferris/hassio-addons/issues>.

Issues with Hawser itself belong upstream at
<https://github.com/Finsys/hawser/issues>.
