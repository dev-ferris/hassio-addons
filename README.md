# dev-ferris Home Assistant Add-ons

[![License: MIT][license-shield]](LICENSE)

This repository is a collection of add-ons for [Home Assistant](https://www.home-assistant.io/)
and can be added directly as an additional add-on source (repository) in the
Home Assistant Add-on Store.

## Add the repository to Home Assistant

To make the add-ons in this repository show up in the Add-on Store, the
repository URL has to be registered in Home Assistant once.

### Option 1: One-click (My Home Assistant link)

Open the following link and Home Assistant will ask whether the repository
should be added — just confirm:

[![Open your Home Assistant instance and show the add add-on repository dialog with a specific repository URL pre-filled.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fdev-ferris%2Fhassio-addons)

### Option 2: Manually via the UI

1. In Home Assistant, open **Settings → Add-ons → Add-on Store**.
2. Click the three-dot menu (**⋮**) in the top right corner and select
   **Repositories**.
3. Paste the following URL and click **Add**:

   ```
   https://github.com/dev-ferris/hassio-addons
   ```

4. Close the dialog. A new section called **dev-ferris Home Assistant Add-ons**
   appears in the Add-on Store with all add-ons available from this repository.

> **Note:** Adding a repository requires the Home Assistant **Supervisor**
> (e.g. Home Assistant OS or Home Assistant Supervised). The Add-on Store is
> not available on Home Assistant Container or Core installations.

## Install an add-on

Once the repository is registered, the individual add-ons can be installed
like any other add-on:

1. Open **Settings → Add-ons → Add-on Store**.
2. Scroll to the **dev-ferris Home Assistant Add-ons** section.
3. Pick the desired add-on and click **Install**.
4. On the **Configuration** tab, adjust the options
   (see the README of the respective add-on).
5. On the **Info** tab, start the add-on and optionally enable
   *Start on boot* and *Watchdog*.

## Available add-ons

| Add-on | Description | Documentation | Build status |
| --- | --- | --- | --- |
| **Hawser** | [![Builder][builder-shield]][builder-workflow] 
Lightweight Go agent that lets [Dockhand](https://github.com/Finsys/dockhand) manage Docker hosts behind firewalls, NATs or in cluster setups. | [hawser/README.md](hawser/README.md) | [![Builder][builder-shield]][builder-workflow] |

The configuration options and any further details are documented in the
README of the respective add-on.

The published container images are hosted on the GitHub Container Registry
under [`ghcr.io/dev-ferris`](https://github.com/dev-ferris?tab=packages).

## Updates

Updates appear automatically in the Add-on Store as soon as a new version of
this repository is published. The update can be applied with a single click
under **Settings → Add-ons**.

## Support

- Issues with the add-ons in this repository should be reported at
  <https://github.com/dev-ferris/hassio-addons/issues>.
- Issues with the underlying upstream software (e.g. Hawser itself) belong in
  the respective upstream project.

## License

This repository is licensed under the [MIT License](LICENSE).

## Acknowledgements

This repository was created with the help of
[Claude](https://claude.com/claude-code), Anthropic's AI assistant.

[builder-shield]: https://github.com/dev-ferris/hassio-addons/actions/workflows/builder.yaml/badge.svg
[builder-workflow]: https://github.com/dev-ferris/hassio-addons/actions/workflows/builder.yaml
[license-shield]: https://img.shields.io/badge/License-MIT-yellow.svg
