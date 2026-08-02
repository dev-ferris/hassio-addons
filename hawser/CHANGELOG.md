# Changelog

## 0.2.46.1

### Fixed

- The add-on could not start: mapping the `LOG_LEVEL` option into the s6
  container environment collided with bashio, which reads the same variable
  name as its own numeric log level. Its arithmetic comparison then failed with
  `warn: unbound variable`, and since bashio runs with `set -o nounset
  -o errexit`, the service script died on its first log line and s6 restarted
  it in a loop. The value is now carried as `HAWSER_LOG_LEVEL` and translated
  back immediately before the agent is executed. `env_vars` can no longer set
  `LOG_LEVEL` either.

### Changed

- Moved the `user` bundle to `/etc/s6-overlay/user-bundles.d`, as required
  since s6-overlay 3.2.3.2. Removes the deprecation warning printed on every
  start.

## 0.2.46

The add-on version now tracks the packaged upstream Hawser version. Add-on
changes that do not move upstream get a fourth component, e.g. `0.2.46.1`.

### Security

- The Docker socket is no longer handed out with more privileges than needed:
  `full_access` (privileged mode), `hassio_api` (Supervisor API) and `udev`
  were removed. `docker_api` alone mounts the socket, which is all Hawser
  uses.
- The `share` folder mapping was removed, and `ssl` is now mounted read-only.
  Compose bind mounts are resolved by the Docker daemon on the host, so the
  add-on never needed write access to `share`.
- The add-on now refuses to start in Standard mode when it would expose an
  unauthenticated Docker API on a network-reachable address. Set `TOKEN`, bind
  to loopback, or opt out explicitly with the new `ALLOW_INSECURE_NO_AUTH`
  option.
- Configuration is validated at startup with actionable error messages:
  Edge mode without a token, a malformed `DOCKHAND_SERVER_URL`, a TLS
  certificate without its key, and certificate paths that do not exist inside
  the add-on are all caught before Hawser starts.
- Warnings are logged for Standard mode without TLS, `ws://` instead of
  `wss://`, `TLS_SKIP_VERIFY` and `ALLOW_INSECURE_NO_AUTH`.
- `env_vars` can no longer override variables that control the add-on itself
  (`PATH`, `LD_PRELOAD`, `LD_LIBRARY_PATH`, `LD_AUDIT`, `HOME`,
  `SUPERVISOR_TOKEN`, `HASSIO_TOKEN`).
- The AppArmor profile now grants an explicit list of capabilities instead of
  all of them, and no longer allows `mount`/`umount`/`remount`. The profile
  name matches the add-on slug.
- The upstream image is pinned to a version instead of `latest`, so builds are
  reproducible and upstream changes are picked up deliberately.
- CI: all GitHub Actions are pinned to commit SHAs, workflow permissions
  default to none with per-job grants, and published images are signed with
  Cosign.

### Added

- `ALLOW_INSECURE_NO_AUTH` and `WELCOME_TIMEOUT` options.
- A Docker health check that probes the agent's `/_hawser/health` endpoint,
  following the configured `PORT`, `BIND_ADDRESS` and TLS settings. Home
  Assistant surfaces it as the add-on state and restarts the add-on when it
  goes unhealthy and the Watchdog is enabled.
- The agent ID is generated once and persisted in `/data/agent_id`. Hawser
  otherwise draws a fresh random ID on every start, which made the agent show
  up as a new host in Dockhand after each restart.
- Real documentation on the add-on's **Documentation** tab (`DOCS.md`), which
  previously only linked to the README.

### Changed

- Base image updated from `16.0.0` to `21.0.1`.
- Upstream Hawser updated from an unpinned `latest` to `0.2.46`.
- Migrated from the deprecated s6-overlay v2 `/etc/services.d` layout to
  s6-rc.d service definitions, with proper crash handling in `finish`.
- Numeric options (`PORT`, the intervals and timeouts) are numbers instead of
  strings, and `PORT` uses the `port` schema type.
- Removed `boot`, `startup` and `host_network` from the configuration; all
  three only repeated the Supervisor's defaults.
- Compose stacks in `/data/stacks` are now included in backups instead of
  being excluded.
- Dropped `tzdata` and `wget` from the image; both are already provided by the
  base image.
- CI builds natively on `amd64` and `aarch64` runners with BuildKit layer
  caching instead of QEMU emulation, and only rebuilds add-ons that changed.
  This replaces the `home-assistant/builder` action, which upstream has
  deprecated and announced for removal.

## 0.1.0

- Initial release of the Hawser add-on
- Packages [Finsys/hawser](https://github.com/Finsys/hawser) on top of the
  Home Assistant add-on base image
- Supports both Standard mode (listening on port 2376) and Edge mode (outbound
  WebSocket via `DOCKHAND_SERVER_URL`)
- Persists Docker Compose stacks under `/data/stacks`
