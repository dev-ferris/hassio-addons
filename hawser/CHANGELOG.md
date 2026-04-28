# Changelog

## 0.1.0

- Initial release of the Hawser add-on
- Packages [Finsys/hawser](https://github.com/Finsys/hawser) on top of the
  Home Assistant add-on base image
- Supports both Standard mode (listening on port 2376) and Edge mode (outbound
  WebSocket via `DOCKHAND_SERVER_URL`)
- Persists Docker Compose stacks under `/data/stacks`
