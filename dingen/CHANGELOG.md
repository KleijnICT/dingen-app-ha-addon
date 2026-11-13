# Changelog

## 1.0.0-alpha.5

A calmer task panel, spaces as groundwork for sharing, lowercase tags and
backups with a safety net. A pre-release; `:latest` points to it. The update
runs a database migration (with a backup first; if it fails the backup is put
back and Dingen stops).

- Backups: every night from 03:00 (in `DINGEN_TIMEZONE`) Dingen writes a
  consistent backup to `DATA_DIR/backups`, while it runs. Kept: 7 daily, 4
  weekly and 3 monthly, plus the 10 newest from before an update. Backup names
  now hold the kind, the moment and the schema version. `DINGEN_BACKUPS=off`
  turns the nightly ones off.
- A failed update no longer leaves a half-migrated database: if a database
  migration or a one-time data conversion fails at start, Dingen puts back the
  backup it made just before, says so in the log and stops, so the previous
  version can be started. Data conversions get a backup first too.
- The `dingen` command in the Docker image: `dingen backup`, `dingen backups`
  and `dingen restore <file>` (only with the server stopped; saves the current
  state first and refuses files that are not a Dingen database or come from a
  newer version).
- Sync after a restore: devices that are ahead of the server load a fresh
  copy instead of missing changes; unsent changes are kept. Protocol 2 pulls
  mark such a space `resync`; protocol 1 pulls get 409 `cursor_ahead`.
- One server per data directory: the server holds a lock on
  `DATA_DIR/dingen.lock` and does not start when another one (or a restore)
  holds it.
- Task panel: a task opens in a panel that slides in from the right (beside
  the list on wide screens, over it on medium widths, full screen on phones).
  With a mouse a click selects a task and a double click or Enter opens it; on
  touch screens a tap opens. With the panel open, arrow keys and clicks switch
  the task. The address bar holds the open task (`?task=`), so a reload or a
  jump from Search keeps it open.
- The panel is calm: the title with a `…` menu (Duplicate, Delete), notes, the
  checklist with a heading and count, then Planning (When; time and reminder,
  deadline and repeat only when set) and Organisation (List with its heading,
  Tags; Assigned only when set). Empty fields are added from a row at the
  bottom. Everything saves as you type.
- Duplicate a task: the copy lands right below the original, with an
  unchecked checklist, without repeat or assignee.
- Projects and areas show completed tasks in their own folded "Completed"
  section at the bottom, instead of under the last heading.
- Reviews: a weekly or monthly review that was never done counts from the day
  the account was created, so a new account is not asked for a review right
  away. The banners on Today can be closed until tomorrow.
- Tags are always lowercase. Existing tags are converted once at start;
  tags that become equal are merged and tasks point to the one that stays (the
  Logbook shows this as done by Dingen).
- Spaces (groundwork for sharing; nothing changes in use yet): all data
  belongs to a space, every account has a personal space, access goes through
  membership. The sync protocol is version 2 with a cursor per space (`GET
  /api/sync/pull?cursor=`); the old `since=` form keeps working for older
  clients. Entities can move between spaces with their contents. The export
  format is version 2 with the spaces you own; version 1 files still import,
  reviews now included. The update runs a database migration (with a backup
  first).

## 1.0.0-alpha.4

Dingen as a Home Assistant app. A pre-release; `:latest` points to it. The
update runs a database migration (with a backup first).

- Home Assistant: Dingen is a Home Assistant app (add-on), from the repository
  KleijnICT/dingen-app-ha-addon. It opens in the sidebar through ingress and
  signs you in with your Home Assistant user; each Home Assistant user gets an
  own account (the first one is the administrator). Options: registration on
  the direct port and the log level; the time zone comes from Home Assistant.
  Port 8080 is optional, for the installed web app, reminders and MCP. Data
  lives in the app's folder and goes into Home Assistant backups. Every release
  appears as an update in Home Assistant with its changelog.
- Settings → Account: set a password (for an account from Home Assistant) or
  change it. `POST /api/auth/password`; `/api/auth/me` reports
  `homeAssistant` and `hasPassword`.
- The web app works under a path prefix (as Home Assistant ingress serves
  it); there it does not install a service worker or offer web push.
- The Docker image starts as root only to give `/data` to the `node` user,
  then runs as `node`; existing installs are unchanged. The update runs a
  database migration (with a backup first).
- Cache headers for `sw.js`, the manifest and the hashed assets are sent
  again; they were lost.

