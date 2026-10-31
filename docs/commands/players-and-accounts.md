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

## Clans

Every account is in exactly one clan. A new account is in the **default clan, ANViL [ANVL]**, until it creates or
joins another. The default clan is always open, needs no invite, and cannot be left, closed or disbanded; the server
owner can rename it, change its tag and set its motto.

- **Where:** console (`clan ...`) and chat (`!clan ...`).
- **Who:** every signed-in account can use `!clan` in chat, admin or not (a banned account cannot). Inside a clan,
  the **founder** and the clan's **admins** manage it. The **server owner** (the console operator and every account
  with the admin role) has the founder's rights over every clan, through `clan as`, and alone can use `clan move`.
- **Names:** a clan name is 1-26 printable ASCII characters, no `#`, no leading, trailing or double spaces, and is
  unique (names ignore case). A name with spaces goes in double quotes where a word follows it
  (`clan create "Iron Lords" IRL`, `clan as "Iron Lords" rename Wardens`). A tag is 1-4 letters or digits; a motto
  is 0-100 printable ASCII characters.
- **Players:** `<player>` is an account name (the account need not be signed in), or `local`.
- Changes are sent to a signed-on game at once; other players see them at their next refresh or sign-in.
- `!anvil clan` shows the usage in chat.

### `clan [info [name]]` · `clan list`

- **What:** `clan` (or `clan info`) shows your clan: whether it is the default clan or a player clan, open or
  invite-only, your rank in it, its member count, tag and motto. `clan info <name>` shows another clan.
  `clan list` lists every clan with its tag.
- **Examples:** `!clan` · `!clan info Iron Lords` · `!clan list`
- **Refuses:** an unknown clan (`No clan by that name.`); extra words after `list` (`usage: clan list`).

### `clan create <name> [tag]`

- **What:** founds a new clan with you as its founder. A new clan is invite-only until `clan open`. Without a tag,
  the first four letters or digits of the name, in upper case, become the tag.
- **Examples:** `!clan create Wardens` · `!clan create "Iron Lords" IRL`
- **Refuses:** an account already in a player clan
  (`That needs the player out of their current clan first (clan leave).`); a name in use (`A clan with that name already exists.`); a bad name or tag; the server console, which is in no
  clan.

### `clan join <name>` · `clan leave`

- **What:** `join` enters an open clan, or an invite-only clan that has invited you. `leave` puts you back in the
  default clan. When the founder leaves, the longest-serving admin becomes founder (else the longest-serving
  member); a clan left with no members is disbanded.
- **Examples:** `!clan join Iron Lords` · `!clan leave`
- **Refuses:** an invite-only clan without a live invite
  (`That clan is invite-only and you have no invite (or it expired after 7 days).`); an account already in a
  player clan; `leave` from the default clan.

### `clan invite|kick|promote|demote <player>`

- **Who:** the clan's founder and admins.
- **What:** `invite` sends an invite that lasts 7 days (up to 32 live invites per clan). `kick` removes a member
  (back to the default clan). `promote` makes a member an admin; `demote` makes an admin a member again.
- **Examples:** `!clan invite alice` · `!clan promote alice` · `!clan kick bob`
- **Refuses:** the founder as a target (`Not allowed: only the clan's founder or admin can do that, and the founder
  cannot be kicked or demoted.`); kicking or demoting an admin unless you are the founder
  (`Only the clan's founder can do that.`); a player not in the clan; an unknown account
  (`No account by that name.`).

### `clan rename <name>` · `clan tag <tag>` · `clan motto [text]` · `clan open` · `clan close` · `clan disband [name]`

- **Who:** the clan's founder and admins; `disband` is the founder's only.
- **What:** rename the clan, change its tag, set its motto (no text clears it), open it to anyone or make it
  invite-only, or disband it (its members go back to the default clan).
- **Examples:** `!clan rename Wardens` · `!clan tag WRD` · `!clan motto Hold the line` · `!clan close` ·
  `!clan disband`
- **Refuses:** a bad name, tag or motto; a name in use; anyone but the founder for `disband`; `close` or
  `disband` on the default clan (`The default clan cannot be left, closed or disbanded, and needs no invite.`).
  On the console, `clan rename`, `clan tag` and `clan motto` change the default clan.

### `clan as <clan> <action ...>`

- **Who:** the server owner.
- **What:** runs `invite`, `kick`, `promote`, `demote`, `rename`, `tag`, `motto`, `open`, `close` or `disband` on that
  clan instead of your own, with the founder's rights.
- **Examples:** `clan as ANViL rename Vanguard` · `!clan as "Iron Lords" kick bob` · `clan as Wardens disband`
- **Refuses:** an account without the admin role
  (`Only the server owner can act on another clan (clan as).`); an unknown clan; a missing action
  (`usage: clan as <clan> <action ...>`).

### `clan move <player> <clan>`

- **Who:** the server owner.
- **What:** puts a player in a clan, regardless of invites. `clan move <player> ANViL` puts them back in the default
  clan.
- **Examples:** `clan move alice Wardens` · `!clan move bob ANViL`
- **Refuses:** anyone else (`Only the server owner can move players between clans.`); a missing player or clan
  (`usage: clan move <player> <clan>`).
