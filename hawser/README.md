# Home Assistant Add-on: Hawser

[Hawser](https://github.com/Finsys/hawser) is a lightweight Go agent that lets
[Dockhand](https://github.com/Finsys/dockhand) manage Docker hosts behind
firewalls, NATs, or in cluster setups.

This add-on packages the upstream image
[`ghcr.io/finsys/hawser`](https://github.com/Finsys/hawser/pkgs/container/hawser)
for use as a Home Assistant add-on. It is modelled after
[alexbelgium/hassio-addons/portainer_agent](https://github.com/alexbelgium/hassio-addons/tree/master/portainer_agent).

> ⚠️ **Warning** — This add-on requires `full_access` and the Docker API in
> order to manage containers, networks, volumes, images and compose stacks on
> the host. Treat it like any other privileged tool: protect the listener
> with a TLS certificate and a token, or use Edge mode.

## Modes of operation

Hawser detects its mode automatically:

| Trigger                                         | Mode      |
| ----------------------------------------------- | --------- |
| `DOCKHAND_SERVER_URL` **and** `TOKEN` are set   | Edge      |
| Otherwise                                       | Standard  |

In **Standard mode** the add-on listens on TCP port `2376` and waits for
Dockhand to connect. In **Edge mode** the add-on dials out via WebSocket and
no inbound port is required.

## Installation

1. Add this repository to Home Assistant: **Settings → Add-ons → Add-on Store
   → ⋮ → Repositories** and paste
   `https://github.com/dev-ferris/hassio-addons`.
2. Install the **Hawser** add-on.
3. Disable *Protection mode* (required for Docker socket access).
4. Configure the options below and start the add-on.

## Configuration

```yaml
PORT: "2376"
LOG_LEVEL: info
BIND_ADDRESS: 0.0.0.0
TOKEN: "change-me"
AGENT_NAME: ""
DOCKHAND_SERVER_URL: ""
TLS_CERT: ""
TLS_KEY: ""
CA_CERT: ""
TLS_SKIP_VERIFY: false
HEARTBEAT_INTERVAL: "30"
REQUEST_TIMEOUT: "30"
RECONNECT_DELAY: "1"
MAX_RECONNECT_DELAY: "60"
env_vars: []
```

| Option                  | Description                                                                 |
| ----------------------- | --------------------------------------------------------------------------- |
| `PORT`                  | Listening port in Standard mode (default `2376`).                           |
| `LOG_LEVEL`             | One of `debug`, `info`, `warn`, `error`.                                    |
| `BIND_ADDRESS`          | Listen address in Standard mode.                                            |
| `TOKEN`                 | Shared secret. Required for Edge mode and recommended for Standard mode.    |
| `AGENT_NAME`            | Human-readable name reported to Dockhand. Defaults to the hostname.         |
| `DOCKHAND_SERVER_URL`   | WebSocket URL of the Dockhand server. Setting this enables Edge mode.       |
| `TLS_CERT` / `TLS_KEY`  | Paths to a server certificate (typically under `/ssl`).                     |
| `CA_CERT`               | CA certificate used to verify the Dockhand server in Edge mode.             |
| `TLS_SKIP_VERIFY`       | Skip TLS verification (insecure, for testing only).                         |
| `HEARTBEAT_INTERVAL`    | Keepalive interval in seconds.                                              |
| `REQUEST_TIMEOUT`       | Request timeout in seconds.                                                 |
| `RECONNECT_DELAY`       | Initial reconnect delay in seconds (Edge mode).                             |
| `MAX_RECONNECT_DELAY`   | Maximum reconnect delay in seconds (Edge mode).                             |
| `env_vars`              | List of `{name, value}` pairs forwarded as environment variables.           |

Compose stacks are persisted in `/data/stacks` inside the container and
survive add-on updates.

## TLS certificates

To run in Standard mode with TLS, copy the certificate and key into the
Home Assistant `ssl` share and reference them via:

```yaml
TLS_CERT: /ssl/hawser.crt
TLS_KEY:  /ssl/hawser.key
```

## Support

Issues with this add-on should be reported to
<https://github.com/dev-ferris/hassio-addons/issues>.

Issues with Hawser itself belong upstream at
<https://github.com/Finsys/hawser/issues>.
