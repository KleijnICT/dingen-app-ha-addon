# Dingen

Dingen is a self-hosted task manager. This app runs it inside Home Assistant.
More about Dingen: <https://dingen.app> and
<https://github.com/KleijnICT/dingen-app>.

## Using it

Start the app and open **Dingen** in the sidebar. You are signed in with your
Home Assistant user; every Home Assistant user gets their own Dingen account
with their own lists. The first user to open Dingen becomes its
administrator.

Inside Home Assistant (also in the companion app on your phone) Dingen runs
without notifications and without installing it as a separate app; those need
the direct address below.

## Direct access (optional)

Under **Configuration → Network** you can give port `8080` a host port. Dingen
is then also reachable at `http://<your-home-assistant>:<port>`, for:

- the web app installed on your phone or computer, with reminders as
  notifications (needs HTTPS, for example through your reverse proxy);
- the MCP server for Claude (`/mcp`) and the API, with an agent token from
  **Settings → Agents**.

On that address you sign in with an e-mail address and password. An account
created through Home Assistant has no password yet: set one in **Settings →
Account** first.

## Options

- `registration`: who may create an account on the direct address.
  `closed` (default): nobody; Home Assistant users get an account anyway.
  `invite`: only with an invitation link. `open`: anyone who can reach it.
- `log_level`: `debug`, `info`, `warning` or `error`.

The time zone is the one of Home Assistant.

## Data and backups

Everything is stored in the app's own data folder and is part of Home
Assistant backups. Home Assistant stops Dingen for a moment while it makes a
backup, so the database is copied in a consistent state.

To move from another Dingen server: **Settings → Data → Export** there, and
**Import** here.

## Updates

Every Dingen release appears as an update in Home Assistant, with its
changelog. Before an update that changes the database, Dingen first writes a
copy to `backups/` in its data folder.
