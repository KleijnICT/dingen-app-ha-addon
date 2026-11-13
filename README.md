# Dingen for Home Assistant

The Home Assistant app (add-on) repository for
[Dingen](https://github.com/KleijnICT/dingen-app), a self-hosted task manager.

[![Add this repository to your Home Assistant.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FKleijnICT%2Fdingen-app-ha-addon)

Or in Home Assistant: open the app store (called add-on store in older versions) under **Settings**, then **⋮ → Repositories**, add
`https://github.com/KleijnICT/dingen-app-ha-addon`, then install **Dingen**.

Needs Home Assistant OS or Supervised (apps run on the Supervisor), on amd64
or aarch64. Available from Dingen 1.0.0-alpha.4.

The app uses the same image as every other Dingen install
(`ghcr.io/kleijnict/dingen`); this repository only describes it for Home
Assistant. Each Dingen release updates `dingen/config.yaml` and
`dingen/CHANGELOG.md` here automatically, and Home Assistant shows the update.

Issues and code: <https://github.com/KleijnICT/dingen-app>.
