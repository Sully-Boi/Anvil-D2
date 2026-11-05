# Server operation

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

Day-to-day console commands, and the server window. Only `save backup` also works in chat. Help (`help`, `!anvil`) is described on
[Chat and console](chat-and-console.md).

---

## `status`

- **Where:** console only. **Who:** console operator.
- **What:** shows the server figures: uptime, players and sessions, logins (ok and failed), blocked addresses and
  accounts, traffic in and out, frames per second, any scheduled shutdown, each connected player and where they are,
  then the requests the server does not implement yet.
- **Arguments:** none.
- **Examples:** `status`
- **Refuses:** any argument (`this command takes no arguments`).

## `log normal|verbose|quiet`

- **Where:** console only. **Who:** console operator.
- **What:** switches what the console shows while the server runs: `normal` plain sentences (sign-ins, activities,
  encounters, checkpoints, Exotic drops, admin results, and problems with what to do), `verbose` every event (time,
  category, event; for debugging) or `quiet` failures only. `anvil-server.log` always gets every event. The console
  starts in the view of the `log` key of `anvil-server.cfg`, or of `--normal`, `--verbose` or `--quiet`.
  `simple` and `advanced` are accepted as the old names of `normal` and `verbose`.
- **Examples:** `log verbose` · `log normal` · `log quiet`
- **Refuses:** any other word or a missing one (`usage: log normal|verbose|quiet`).

## `players`

- **Where:** console only. **Who:** console operator.
- **What:** lists the players and game connections (connection numbers are what `kick <conn>` takes).
- **Arguments:** none.
- **Examples:** `players`
- **Refuses:** any argument.

## `unsupported`

- **Where:** console only. **Who:** console operator.
- **What:** lists the requests from the game that the server does not implement yet: how often, when last seen, and
  whether each was answered (empty, refused or unavailable), accepted, or closed the connection.
- **Arguments:** none.
- **Examples:** `unsupported`
- **Refuses:** any argument.

## `save`

- **Where:** console only. **Who:** console operator.
- **What:** writes the character and settings stores to disk now.
- **Arguments:** none.
- **Examples:** `save`
- **Refuses:** any argument (other than `backup`, below). In chat, `!save` alone answers
  `usage: save backup [label] (the console's save alone writes the stores now)`.

## `save backup [label]`

- **Where:** console and chat (`!save backup`). **Who:** admin.
- **What:** copies the live store files into a new folder `<data>\backups\<stamp>[-label]`. Files that do not exist
  are skipped. To restore, stop the server, then copy the files back into the data folder.
- **Arguments:** `[label]`: 1-24 letters, digits, `_` or `-`.
- **Examples:** `save backup` · `save backup before-reset` · `!save backup raid_test`
- **Refuses:** a bad label (`save backup: the label is 1-24 letters, digits, '_' or '-'`); a backup already
  waiting (`A backup is already waiting; try again in a moment.`); a folder that already exists (one per second
  and label).

## `config reload`

- **Where:** console only. **Who:** console operator.
- **What:** reads `anvil-server.cfg` again. `max_players`, `log` and `watch_config` change at once (lowering
  `max_players` never signs anyone out; it only refuses new sign-ins until fewer are on). The other keys keep their
  running value until the next start. A bad file changes nothing. `anvil-server --config-template` lists every key.
  `rules reload` does the same for `anvil-rules.cfg` (see [Server settings](server-settings.md)).
- **Arguments:** none.
- **Examples:** `config reload`
- **Refuses:** any other word (`usage: config reload`).

## `gui`

- **Where:** console only. **Who:** console operator.
- **What:** shows the server window again after it was closed while the server kept running. Not available with
  `--headless` or `gui = off` in `anvil-server.cfg`.
- **Arguments:** none.
- **Examples:** `gui`
- **Refuses:** any argument (`usage: gui`).

## `shutdown [seconds]`

- **Where:** console only. **Who:** console operator.
- **What:** stops the server. With seconds, after a countdown that the server announces. `shutdown`, `shutdown 0`
  and `shutdown now` stop it at once.
- **Arguments:** `[seconds]`: 0..86400 (one day), or `now`.
- **Examples:** `shutdown` · `shutdown 300` · `shutdown now`
- **Refuses:** a number out of range or another word (`usage: shutdown [seconds 0..86400]|cancel`).

## `shutdown cancel`

- **Where:** console only. **Who:** console operator.
- **What:** cancels a scheduled shutdown.
- **Examples:** `shutdown cancel`
- **Refuses:** nothing scheduled (`No shutdown is scheduled.`).

---

## The server window

`anvil-server.exe` opens a management window next to the console unless it is started with `--headless` (or
`--no-gui`) or `gui = off` is set in `anvil-server.cfg`. If the window cannot be created the server runs with the
console only. Its pages are Dashboard, Logs, Players, Inventory, Items, Moderation, Staff, Config, Backups and
Console:

- **Dashboard, Logs:** the server figures and the log.
- **Players, Inventory, Items, Moderation, Staff:** accounts and their characters, the items a character or the vault
  holds (move, equip, lock or change the count of an item), an item browser, bans and mutes, and the staff roles.
- **Config:** every `anvil-rules.cfg` and `anvil-server.cfg` key with its range and help, with the restart keys marked.
  A restart button saves and restarts the server after a countdown.
- **Backups:** the backups in the data folder, a button to make one now, and restoring one at the next start.
- **Console:** the console commands on this page, with a global search (Ctrl+K) and page shortcuts (Ctrl+1 to Ctrl+9,
  Ctrl+0). Commands that would ask a question or a password at the terminal (`accounts add`, `accounts passwd`,
  `accounts remove`, the guided `announce`) are refused there with the reason; use the Players page or the terminal.

Closing the window can leave the server running; type `gui` to open it again.
