# Home Assistant Add-on: Hawser

[Hawser](https://github.com/Finsys/hawser) is a lightweight Go agent that lets
[Dockhand](https://github.com/Finsys/dockhand) manage Docker hosts behind
firewalls, NATs, or in cluster setups.

## Installation

1. Add this repository to Home Assistant: **Settings → Add-ons → Add-on Store
   → ⋮ → Repositories** and paste
   `https://github.com/dev-ferris/hassio-addons`.
2. Install the **Hawser** add-on.
3. Disable *Protection mode* on the add-on's **Info** tab. This is required so
   the Supervisor mounts the Docker socket into the add-on.
4. Set at least `TOKEN` on the **Configuration** tab (see below), then start
   the add-on.

## Security

This add-on proxies the Docker socket of your Home Assistant host. Anyone who
can talk to it can start containers, mount host paths and therefore take over
the machine. Two things protect it:

- **`TOKEN`** — a shared secret Dockhand has to send with every request.
  The add-on refuses to start in Standard mode on a network-reachable address
  when no token is set. If you really need that (isolated lab network), set
  `ALLOW_INSECURE_NO_AUTH` to `true`.
- **TLS** — without `TLS_CERT`/`TLS_KEY`, the token travels in clear text on
  every request. Configure TLS whenever the port is reachable from a network
  you do not fully control, or use Edge mode with `wss://`.

The add-on runs unprivileged. It requests the Docker API only — no
`full_access`, no Supervisor API, no udev, and the only host folder it can see
is `ssl`, read-only.

## Modes of operation

Hawser picks its mode automatically:

| Trigger                                        | Mode     |
| ---------------------------------------------- | -------- |
| `DOCKHAND_SERVER_URL` **and** `TOKEN` are set   | Edge     |
| Otherwise                                       | Standard |

In **Standard mode** the add-on listens on TCP port `2376` and waits for
Dockhand to connect. In **Edge mode** the add-on dials out over a WebSocket and
no inbound port is required — clear the host port under **Network** in that
case. A small unauthenticated health endpoint stays available on `PORT` in both
modes; the add-on Watchdog uses it.

## Configuration

```yaml
PORT: 2376
LOG_LEVEL: info
BIND_ADDRESS: "0.0.0.0"
TOKEN: "change-me"
AGENT_NAME: ""
DOCKHAND_SERVER_URL: ""
TLS_CERT: ""
TLS_KEY: ""
CA_CERT: ""
TLS_SKIP_VERIFY: false
ALLOW_INSECURE_NO_AUTH: false
HEARTBEAT_INTERVAL: 30
REQUEST_TIMEOUT: 30
RECONNECT_DELAY: 1
MAX_RECONNECT_DELAY: 60
WELCOME_TIMEOUT: 30
hosts_entries: []
env_vars: []
```

| Option                   | Description                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------- |
| `PORT`                   | Port the agent listens on (default `2376`). Turn the Watchdog off if you change it.           |
| `LOG_LEVEL`              | One of `debug`, `info`, `warn`, `error`.                                                      |
| `BIND_ADDRESS`           | Listen address. `127.0.0.1` accepts local connections only (turn the Watchdog off).           |
| `TOKEN`                  | Shared secret. Required for Edge mode and for any network-reachable Standard mode listener.   |
| `AGENT_NAME`             | Human-readable name reported to Dockhand. Defaults to the hostname.                           |
| `DOCKHAND_SERVER_URL`    | WebSocket URL of the Dockhand server (`ws://` or `wss://`). Setting this enables Edge mode.    |
| `TLS_CERT` / `TLS_KEY`   | Paths to a server certificate and key, both under `/ssl`. Only valid together.                 |
| `CA_CERT`                | CA certificate used to verify the Dockhand server in Edge mode.                               |
| `TLS_SKIP_VERIFY`        | Skip TLS verification (insecure, for testing only).                                           |
| `ALLOW_INSECURE_NO_AUTH` | Allow a network-reachable Standard mode listener without a token. Insecure.                   |
| `HEARTBEAT_INTERVAL`     | Keepalive interval in seconds.                                                                |
| `REQUEST_TIMEOUT`        | Request timeout in seconds.                                                                   |
| `RECONNECT_DELAY`        | Initial reconnect delay in seconds (Edge mode).                                               |
| `MAX_RECONNECT_DELAY`    | Maximum reconnect delay in seconds (Edge mode).                                               |
| `WELCOME_TIMEOUT`        | Seconds to wait for the server's welcome message after connecting (Edge mode).                |
| `hosts_entries`          | List of `{name, ip}` pairs written to `/etc/hosts`. See *Name resolution*.                    |
| `env_vars`               | List of `{name, value}` pairs forwarded as environment variables.                             |

Options left empty keep Hawser's own defaults. `env_vars` cannot override
variables that control the add-on itself (`PATH`, `LD_PRELOAD`,
`SUPERVISOR_TOKEN`, and similar) — the add-on refuses to start instead.

## Name resolution

Add-ons do not use the host's `/etc/resolv.conf`. They resolve names through
Docker's embedded resolver (`127.0.0.11`), which forwards to the Home Assistant
DNS service, which in turn forwards to its configured upstream servers. Local-
only domains — `.home`, `.lan`, `.fritz.box`, names your router or Pi-hole
hands out — are frequently unknown up there, and the failure shows up in the
log as a reconnect loop:

```text
Connection failed: WebSocket dial failed: dial tcp:
lookup dockhand.home on 127.0.0.11:53: server misbehaving
```

The add-on checks the host in `DOCKHAND_SERVER_URL` at startup and prints an
explicit warning when it cannot be resolved. Three ways to fix it, cheapest
first:

1. **Use the IP address** in `DOCKHAND_SERVER_URL`:
   `ws://192.168.1.20:3000/api/hawser/connect`. Note that a `wss://`
   certificate is usually issued for the name, not the address.
2. **Map the name statically** with `hosts_entries`. The pairs are appended to
   `/etc/hosts` inside the add-on, which takes precedence over DNS:

   ```yaml
   DOCKHAND_SERVER_URL: ws://dockhand.home:3000/api/hawser/connect
   hosts_entries:
     - name: dockhand.home
       ip: 192.168.1.20
   ```

3. **Teach Home Assistant about your local DNS server**, which fixes it for
   every add-on at once. On the host:

   ```shell
   ha dns options --servers dns://192.168.1.1
   ha dns restart
   ```

   Replace the address with your router or local DNS server. `ha dns info`
   shows the current setting.

## TLS certificates

Copy the certificate and key into the Home Assistant `ssl` share and reference
them by path:

```yaml
TLS_CERT: /ssl/hawser.crt
TLS_KEY: /ssl/hawser.key
```

The add-on checks at startup that the referenced files exist and stops with a
clear message if they do not.

## Compose stacks

Stack files sent by Dockhand are written to `/data/stacks` and Compose runs
there. Note that the Compose *client* runs inside the add-on while the Docker
*daemon* runs on the host: bind mount sources in a stack are therefore resolved
against the host filesystem, not against the add-on's. Use absolute host paths
(for example `/mnt/data/supervisor/share/...` on Home Assistant OS) rather than
relative ones or `/share`.

Stacks that use `build:` work as well; the image contains the buildx plugin, so
BuildKit features such as build secrets and additional build contexts are
available.

## Persistent state

- **Compose stacks** are stored in `/data/stacks` and survive add-on updates.
  They are included in Home Assistant backups.
- **Agent ID**: Hawser generates a random agent ID on every start unless one is
  supplied. The add-on generates one on first start and keeps it in
  `/data/agent_id`, so the agent keeps its identity in Dockhand across
  restarts and updates.

## Watchdog

The image carries a Docker health check that asks the agent's
`/_hawser/health` endpoint whether it can still reach the Docker daemon. It
follows your configured `PORT`, `BIND_ADDRESS` and TLS settings, so it keeps
working in every supported configuration, including Edge mode.

Home Assistant shows the result as the add-on's state and, if you enable
**Watchdog** on the **Info** tab, restarts the add-on when it goes unhealthy.

## Support

Issues with this add-on belong at
<https://github.com/dev-ferris/hassio-addons/issues>.

Issues with Hawser itself belong upstream at
<https://github.com/Finsys/hawser/issues>.
