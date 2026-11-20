# Changelog

## 1.0.0-beta.1

The first beta: phases 1 and 2 of the roadmap are complete. From this release
on the sync protocol stays compatible until 2.0; the MCP tools may still
change until 1.0. A pre-release; `:latest` points to it. The update runs a
small database migration (with a backup first) and migrates databases from
every earlier release. Reload the web app to get the new version; a tab that
was left open on an older version must be reloaded too. Update the Claude
plugin for its updated skill, and pull the new image for a stdio MCP server
(`dingen-mcp`): web apps and stdio MCP servers from 1.0.0-alpha.9 and earlier
can no longer sync.

- Sync protocol 4. Clients that speak an older protocol (the web app and the
  stdio MCP server from 1.0.0-alpha.9 and earlier) are refused with
  `426 client_outdated` and must update. From now on the web app reloads by
  itself when this happens, and shows "This version of Dingen is out of
  date" if reloading does not help; the stdio MCP server says to update. The
  MCP server over `/mcp` runs in Dingen itself and needs nothing. Gone: pulls
  with `since=` (protocol 1), the conversion of `evening` and `time` for
  protocol 2 clients, and the `seq` over all spaces in snapshot and push
  answers.
- From 1.0.0-beta.1 until 2.0 the sync protocol only grows (new optional
  fields with a default, new entity types); a contract test pins it. The MCP
  server info now reports the Dingen version with the protocol, for example
  `1.0.0-beta.1+protocol.4`; `/api/health` reports `version` and
  `syncProtocol`.
- The step-by-step review of before 1.0.0-alpha.8 is gone as an entity type.
  The database migration `0013_drop_reviews` deletes the stored review runs;
  older export files with reviews still import, without them.
- Every release is now tested to migrate a database of every alpha release
  (1.0.0-alpha.1 to alpha.9) and keep its data.
- The Claude plugin's skill: tasks are named by id (projects, areas, headings
  and tags also by title), and what to do when the plugin is too old.
- Documentation: the README, the roadmap and the contribution notes describe
  the beta.

## 1.0.0-alpha.9

A calmer redesign with one layout for every pane, administration in the app,
password reset and two-step verification. A pre-release; `:latest` points to
it. The update runs two database migrations (with a backup first) and a third
small one for push notifications. Optional: set `SMTP_*` and
`DINGEN_PUBLIC_URL` for mail; Home Assistant users get these as app options.
Registration is now chosen in Settings → Administration; your current
setting carries over. Reload the web app to get the new version.

- Mail: set `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD`,
  `SMTP_FROM` and `SMTP_SECURE`, and `DINGEN_PUBLIC_URL` (the address people
  open Dingen on, used for links in mail). Settings → Administration shows
  whether mail is set up and sends a test mail.
- Password reset: with mail and `DINGEN_PUBLIC_URL` set up the sign-in
  screen offers "Forgot password?": a link that works once, for an hour, mailed in English or
  Dutch, without telling whether an address has an account. An
  administrator can also make a one-time reset link (24 hours) for a user to
  pass on by hand. A reset signs the account out everywhere; agents' tokens
  keep working.
- Two-step verification with an authenticator app, optional for everyone
  (Settings → Account), with ten recovery codes. Administration shows who
  has it on and can turn it off for someone who lost their phone.
- Administration in Settings, for administrators (replaces the Invitations
  tab): the users with when they joined and were last active; make someone
  an administrator; block an account (signed out everywhere, no sign-in, no
  two-step step or reset link, its API and agent tokens and Home Assistant
  access refused, no notifications) and unblock it; delete a blocked
  account permanently with all its own data, after typing its email address
  and with a backup first. Nobody can block, delete or unadmin themselves or
  the last administrator. Everything under `/api/admin` is for
  administrators in a browser session (or Home Assistant) only; agent and
  API tokens are refused.
- Registration in the app: who may create an account (nobody, with an
  invitation, anyone) is chosen in Administration and stored in the
  database; `DINGEN_REGISTRATION` is only the starting value, and an
  existing server keeps its current setting.
- Backups in the app: the backups are listed with date, kind and size; back
  up now, download one, or restore one after typing "restore". A restore
  makes its own backup first and restarts the server's database in place;
  devices load a fresh copy.
- Two database migrations, with a backup first: `0011_mail_reset_totp`
  (reset links, recovery codes and two-step settings) and
  `0012_blocked_users` (`blocked_at` on `users`).
- A calmer, more consistent look (the first step of a redesign): one set of
  design tokens for space, type, radius, shadow and motion, a finished dark
  theme, quiet buttons with one primary per screen, one icon button, one
  style for the active item, soft menu hovers and section headings without
  capitals. The rules are in `DESIGN.md`.
- One layout for every pane: the sidebar, the review list, the content, the
  task panel, Settings and Search each have the same 48px head with a title
  and icon buttons on the right, so their first lines line up. On a desktop
  the list's actions (new task, When, Move, Tags, …) and the page's own
  (filter, focus, `…`) move from the bottom bar into the content's head; on
  a phone the bottom bar stays. Search sits in the sidebar's head. A project
  in an area shows a quiet "Area ›" path in the head, and the task panel
  shows where its task lives. In the Review the sidebar folds to a rail of
  icons and the review list takes its place; each item says when it was last
  reviewed and how many tasks are open.
- The sidebar folds to a rail of icons with a button in its head, and
  unfolds with the same button on top of the rail (or with `[`). The rail
  shows the lists and the areas; the choice stays on this device, and
  opening a list or area from the rail keeps it folded. In the Review the
  sidebar still folds by itself, but the button unfolds it there too. Search
  in the sidebar's head is a plain magnifying glass. Phones keep the drawer.
- Menus and popovers are one kind of sheet: the same surface everywhere,
  sections with a small heading (Planning, Organisation; When, Daypart),
  icons in front of menu items, the delete action last under a line. On a
  phone they come up from the bottom with a grab handle, and close with a
  tap beside them or a swipe down. Empty lists and pages all show a short
  title with one sentence, and the Logbook no longer shows "Loading…" as if
  it were empty.
- Push notifications arrive in the language of the device: when you turn on
  reminders, the device sends its app language along, and again when you
  change the language. Reminders and "an agent assigned you a task" then come
  in that language (English for devices that did not tell yet; open Settings
  once to update them). The update runs a small database migration (a
  `language` column on `push_subscriptions`), with a backup first.
- The server keeps the change log for a year: older changes are removed
  daily, after the nightly backup (and at startup when overdue), in small
  batches. Your tasks, projects and exports are not affected; the activity
  log (Logbook) no longer goes back more than a year. A device that was
  offline for longer than that loads its data afresh instead of syncing the
  missing changes.

## 1.0.0-alpha.8

A new, simpler review: every project and area has its own review frequency,
and the Review page lists what is due beside the sidebar. Also: Someday items
at the bottom of project and area pages, dragging a whole selection, and
Anytime without what is in Today. A pre-release; `:latest` points to it. The
update runs a small database migration (a column on `users`), with a backup
first. The daily, weekly and monthly reviews are gone; the MCP review tools
changed, so update the MCP plugin. Reload the web app to get the new version.

- A new, simpler review. Every project and area has a review frequency
  (every N days, weeks, months or years; every week unless you change it, in
  the `…` menu of the project or area). Once that time has passed since its
  last review, it shows on the Review page: a list of what is due that slides
  out of the sidebar, in sidebar order, with the selected project or area
  beside it, fully editable. An area shows only its own tasks; its projects
  have their own review. "Mark reviewed" moves on to the next one. Someday
  projects come along too.
  The daily, weekly and monthly reviews with their steps are gone, and so are
  the review signals and the review notice in Today. No database migration.
- MCP: `review_queue` lists what is due for review and `mark_reviewed` marks
  a project or area reviewed; `update_project` and the area tools take a
  review frequency. The step tools (`start_review`, `get_review`,
  `complete_review_step`, `complete_review`) are gone.
- Today shows a banner when tasks came in by themselves, because the date
  they were planned for arrived: "3 new tasks in Today", with a dot on each.
  What you move to Today yourself does not count. OK hides it on every device.
  Database migration: a column on `users`.
- A new project's title asks for the desired outcome.
- Dragging one of several selected tasks moves all of them, wherever you
  drop them (a list, project, area, heading or a place in the list), in
  their order; the drag shows how many. Moving a selection keeps its order.
- Anytime no longer shows what is in Today: a task is in one of the two.
  The project and area pages still show all their tasks.
- On a project's and an area's page, Someday tasks (and an area's Someday
  projects) sit together at the bottom under "Someday", before Completed.
  The circle in front of a Someday task or project is drawn dashed.

## 1.0.0-alpha.7

The Inbox now holds everything that is not processed yet, and the weekly
review walks areas and projects together. A pre-release; `:latest` points to
it. No database migration. After the update, existing tasks without a project
or area show in the Inbox. Reload the web app and update the MCP plugin to
get the new version.

- The Inbox holds every task that is not processed yet: a task leaves it once
  it has a project or area and a When. A task with only a date stays in the
  Inbox and also shows on its date in Today and Upcoming. Existing tasks
  without a project or area now show in the Inbox.
- A new task made on a project's or area's page goes into that project or
  area (Anytime), after its last task; from the other lists it still goes to
  the Inbox.
- Weekly review: Projects and Areas are one step, "Areas and projects": per
  area a card for the area itself, then one for each of its projects. The
  "Loose tasks" step is gone; those tasks are in the Inbox now.
- A project in the weekly review shows its headings and the same order as the
  project's page, and its tasks can be dragged.

## 1.0.0-alpha.6

Dayparts and estimated durations, a calmer task panel and page header, and a
lot of everyday work: selecting several tasks, a drawer on phones, focus mode,
tags in Settings and converting a task into a project. A pre-release;
`:latest` points to it. The update runs a database migration with sync
protocol 3 (with a backup first; if it fails the backup is put back and
Dingen stops). Older apps and the previous MCP plugin keep working; reload
the web app to get the new version.

- A calmer page header on lists, projects and areas: the title with a few quiet icon buttons on its right (Filter, Focus on Today, and the `…` menu, which on Today holds My order / By project) and one muted line under it (on Today the date and the day total). Tag and duration filters live in one Filter popover; a filter that is on shows as a small chip with × under the title.
- Links in notes (http, https and www.) are clickable and open in a new tab; click elsewhere on the text to edit. The notes of a project in a review show links too.
- Drag the edge of the sidebar or the task panel (beside the list) to make it wider or narrower, with mouse, finger or pen; double-click or double-tap puts the default back, and the arrow keys move a focused edge in steps. The widths are kept per device, and the list always keeps room.
- Select several tasks at once (web): Shift-click selects a range, Cmd/Ctrl-click
  adds or removes one, Shift+Arrow extends the selection; on a touch screen a long
  press starts selection mode, where taps add and remove tasks. The toolbar shows
  how many are selected and When, Move and the new Tags button act on all of them;
  Backspace/Delete (or the toolbar's Delete button) deletes them after one
  confirmation. Escape clears the selection.
- Settings → Calendars: removing a calendar now goes through a `…` menu with a confirmation, like deleting a project.
- MCP tools `delete_project` and `delete_area`, only for an agent with Manage
  on that project or area (or on Everything): they delete like the app does,
  including headings, tasks and, for an area, its projects. Without Manage the
  tool refuses with a clear message.
- Focus mode in Today (web): a Focus button in the header, or the F key, shows
  only today's tasks at full width: no calendar events, review banners,
  sidebar or empty groups. Escape leaves it. Remembered per device.
- Push notification when an agent assigns you a task: "Claude assigned you a
  task" with the task title, on the devices where notifications are on.
  Clicking it opens the task. Nothing is sent when you assign a task
  yourself, when an agent sends the same assignee again, or when an agent
  completes a task (that stays in the Logbook).
- On phones the sidebar is a drawer from the left instead of the bottom
  navigation bar: the menu button top left or a swipe from the left screen
  edge opens it, with the same lists, areas, projects, New List and Settings
  as on the desktop. Choosing a list, Escape or tapping beside it closes it.
- Settings → Tags: every tag as a tree with its number of open tasks. Create a
  tag (always lowercase), rename it inline (a name already used in the same
  space is refused), move it under another tag or to the top level, and
  delete it: it comes off its tasks and projects, which stay, and the tags
  directly under it move to the top level. The settings tabs fit on one row
  again.
- Dayparts and estimated duration, groundwork (core and server): a task has a
  daypart (morning, afternoon or evening) instead of This Evening and a time
  of day, and an optional estimated duration. A reminder keeps its own exact
  time and no longer follows the task. The update converts existing tasks
  once, with a backup first: This Evening becomes the evening daypart, a time
  becomes its daypart, and a task with a time but no reminder gets a
  reminder at that time, so no notification is lost. Sync protocol 3 and
  export format 3; older clients and export files keep working and are
  converted.
- Dayparts in the web app: the When picker offers Today, Tomorrow, Next week
  (the Monday of next week), a date and Someday, and under a date a choice of
  no daypart, morning, afternoon or evening; the separate This Evening choice
  is gone. Today shows the tasks without a daypart first, then Morning,
  Afternoon and Evening when they hold something, with calendar events under
  the daypart of their start time; dragging a task into a daypart sets it.
  The estimated time shows per daypart and for the whole day. Upcoming and the
  task panel show the daypart.
- Estimated duration in the web app: add a duration to a task from the Add
  row of the task panel (5m, 15m, 30m, 1h, 2h, or type any value such as 45
  or 1h30); the row shows it small and grey on the right. Today and Anytime
  get a duration filter next to the tags (at most 5m, 15m, 30m or 1h), kept
  per device; tasks without a duration drop out while it is on. The activity
  overview names duration changes.
- MCP tools and plugin: `create_task` and `update_task` take a `daypart`
  (morning, afternoon, evening) and a `duration` in minutes; `list_tasks`
  gets a `max_duration` filter and shows daypart and duration. The old
  `evening` and `time` fields still work for this version and are converted
  (a time becomes its daypart plus a reminder); they are marked deprecated.
  The Dingen skill describes dayparts, reminders as the one exact time, and
  durations.
- The task panel stays open when its task leaves the list, for example after
  choosing Tomorrow in Today, so more fields can be filled in; an OK button
  at the bottom closes it.
- Convert a task to a project, from the task panel's menu or with the MCP
  tool `convert_task_to_project`: the project takes the task's title, notes,
  area, tags, plan date and deadline, each checklist item becomes a task in
  it (ticked items completed), the task is deleted and the project opens.
- A calmer task panel: every field, including notes, checklist, When, list
  and tags, only shows once it has a value; the others are in the Add row,
  and adding one opens it (the When and list choices open right away). A
  section heading only shows with a field in it. A new task now always starts
  in the Inbox, from any list, project or area (the + button is on every list,
  the Logbook too); choosing a When or a list in the panel moves it there.
- Long project and area titles wrap onto more lines instead of being cut off;
  the Logbook's filters (kind, who, period) moved into the filter button,
  with a small chip for each filter that is on.

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

