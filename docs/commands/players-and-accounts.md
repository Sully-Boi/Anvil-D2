# Players and accounts

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

**Account names** are 1-32 characters: letters, digits, `_` or `-`. Names ignore case (`Bob` and `bob` are the
same account). **Passwords** are 8-128 characters and are never shown while you type them.

The same account changes are also on the command line: see [Command line](command-line.md).

---

## `accounts [list]`

- **Where:** console only.
- **Who:** console operator.
- **What:** lists every account with its creation time (UTC) and its flags: `admin`, `BANNED [until ...] (reason)`,
  `muted [until ...]`.
- **Arguments:** none. `accounts` and `accounts list` are the same.
- **Examples:** `accounts` · `accounts list`
- **Refuses:** extra words (`usage: accounts [list] | accounts add|passwd <name> | accounts remove <name> [--delete-data]`).

## `accounts add <name>`

- **Where:** console only.
- **Who:** console operator.
- **What:** creates an account. The console asks for the password twice; it is not shown.
- **Arguments:** `<name>`: a new account name (see the rules above).
- **Examples:** `accounts add alice`
- **Refuses:** a name that already exists (`Account <name> already exists (names ignore case).`); a password
  shorter than 8 or longer than 128 characters (`Passwords are 8-128 characters; nothing was changed.`).

## `accounts remove <name> [--delete-data]`

- **Where:** console only.
- **Who:** console operator.
- **What:** removes an account after a `y/N` question. Its data folder is kept unless you add `--delete-data`.
- **Arguments:** `<name>`: an existing account. `--delete-data`: also delete its data folder.
- **Examples:** `accounts remove alice` · `accounts remove alice --delete-data`
- **Refuses:** an unknown account (`No account named <name>.`). Anything but `y` at the question cancels.

## `accounts passwd <name>`

- **Where:** console only.
- **Who:** console operator.
- **What:** changes an account's password. The console asks for it twice; it is not shown.
- **Arguments:** `<name>`: an existing account.
- **Examples:** `accounts passwd alice`
- **Refuses:** an unknown account; a password outside 8-128 characters.

## `admins`

- **Where:** console and chat (`!admins`).
- **Who:** admin.
- **What:** lists the admin accounts, marking any that are `(banned)` or `(muted)`.
- **Arguments:** none.
- **Examples:** `admins` · `!admins`
- **Refuses:** extra words (`usage: admins`).

## `accounts admin <name> on|off`

- **Where:** console only (also `anvil-server --set-admin <name> on|off`).
- **Who:** console operator.
- **What:** grants (`on`) or removes (`off`) the admin role. Admins can use the chat commands.
- **Arguments:** `<name>`: an existing account. `on` or `off`.
- **Examples:** `accounts admin alice on` · `accounts admin alice off`
- **Refuses:** chat (`Only the server console can grant or remove the admin role.`); an unknown account
  (`No account named <name>.`); a bad form (`usage: accounts admin <name> on|off`). Granting a role the account
  already has answers `<name> is already an admin.` and changes nothing.

## `characters [local]`

- **Where:** console and chat (`!characters`).
- **Who:** admin.
- **What:** lists the local player's characters: slot, class, which one is selected, the loadout
  (fresh tutorial or diagnostic), the first-mission checkpoint area and the mission weapon grants. The first line
  also shows the Guardian Rank.
- **Arguments:** optional `local` (the only player store).
- **Examples:** `characters` · `!characters local`
- **Refuses:** any player other than `local`; more than one word after the command (`usage: characters [local]`).

## `name`

- **Where:** console and chat (`!name`).
- **Who:** admin.
- **What:** shows the player's display name and whether it was set by the operator or is the default.
  The name itself is shown to you only, never written to the log.
- **Arguments:** none.
- **Examples:** `name` · `!name`
- **Refuses:** needs a running server. Not available from chat for a remote-play account.

## `name set <name>`

- **Where:** console and chat (`!name set`).
- **Who:** admin.
- **What:** sets the display name. A signed-on game updates the orbit header at once; chat and the roster use the
  new name from the next sign-in.
- **Arguments:** `<name>`: 1-26 printable ASCII characters, no `#`, no leading, trailing or double spaces.
  Spaces inside the name are allowed.
- **Examples:** `name set Ashen Wolf` · `!name set Kell Breaker`
- **Refuses:** a name that breaks the rules
  (`name: 1-26 printable ASCII characters, no '#', no leading, trailing or double spaces`); a missing name
  (`usage: name | name set <name>`). Setting the current name answers `No change: that is already the display name.`
