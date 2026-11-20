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

Registration can also be chosen in Dingen under **Settings → Administration**.
The option above is only the starting value: once a choice is stored there,
the option no longer applies.

### Mail (optional)

With a mail server Dingen can send "Forgot password?" links and a test mail
from **Settings → Administration**. Fill in:

- `public_url`: the address you open Dingen on directly, for example
  `https://dingen.example.com`. Links in mail always use it; without it
  "Forgot password?" stays off.
- `smtp_host` and `smtp_from` (for example `Dingen <dingen@example.com>`):
  both are needed to turn mail on.
- `smtp_port` (587 by default, 465 with `smtp_secure`), `smtp_secure` (TLS
  from the start), `smtp_user` and `smtp_password` if your mail server wants
  them. The password is never shown in the log.

Inside Home Assistant you are signed in by Home Assistant, so you do not need
"Forgot password?" there; it is for the direct address.

The time zone is the one of Home Assistant.

## Data and backups

Everything is stored in the app's own data folder and is part of Home
Assistant backups. Home Assistant stops Dingen for a moment while it makes a
backup, so the database is copied in a consistent state.

Dingen also makes its own backups in `backups/` in that folder: every night
from 03:00, and before every update that changes the database. An
administrator sees them in **Settings → Administration → Backups**, and can
make one now, download one, or restore one (after typing `restore`); a
restore works without leaving Home Assistant and makes a backup of the
current state first.

To move from another Dingen server: **Settings → Data → Export** there, and
**Import** here.

## Updates

Every Dingen release appears as an update in Home Assistant, with its
changelog. Before an update that changes the database, Dingen first writes a
copy to `backups/` in its data folder; if a migration fails, it puts that copy
back and stops with a message in the log.
