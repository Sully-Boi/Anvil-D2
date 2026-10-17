# Moderation

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

All moderation commands take an account name (1-32 letters, digits, `_` or `-`; case is ignored).
In chat, an admin cannot moderate another admin: `<name> is an admin; only the server console can do that.`
Every action is written to `anvil-server.log`.

Durations are in **minutes**, 1 to 5256000 (ten years). Leave the minutes out for a permanent ban or mute.

---

## `ban <name> [minutes] [reason]`

- **Where:** console, chat (`!ban`) and command line (`--ban <name> [minutes] [reason]`).
- **Who:** admin (chat: not against another admin).
- **What:** refuses the account's logins and signs it out at once. Without minutes the ban is permanent.
- **Arguments:**
  - `<name>`: the account.
  - `[minutes]`: 1..5256000. A number right after the name is always read as the minutes.
  - `[reason]`: the rest of the line, at most 64 printable ASCII characters.
- **Examples:** `ban griefer 60 spawn camping` · `!ban griefer` (permanent) · `ban griefer 1440`
- **Refuses:** an unknown account (`No account named <name>.`); minutes out of range
  (`ban: minutes must be 1..5256000 (ten years); leave it out for a permanent ban`); a long or non-ASCII reason
  (`ban: the reason is at most 64 printable ASCII characters`).
- **Note:** the reply names the end time in UTC, for example `Banned griefer for 60 minutes (until 2026-10-11 14:05 UTC)`.
  From the command line the ban also works while the server runs: it signs the account out within a second.

## `unban <name>`

- **Where:** console, chat (`!unban`) and command line (`--unban <name>`).
- **Who:** admin (chat: not for another admin).
- **What:** lifts a ban.
- **Arguments:** `<name>`.
- **Examples:** `unban griefer` · `!unban griefer`
- **Refuses:** an unknown account. An account that is not banned answers `<name> is not banned.`

## `mute <name> [minutes]`

- **Where:** console and chat (`!mute`).
- **Who:** admin (chat: not against another admin).
- **What:** records a chat mute on the account, permanent without minutes. The mute shows in `admins` and in the
  account list. This build stores the mute but does not yet block the account's chat messages.
- **Arguments:** `<name>`; `[minutes]`: 1..5256000.
- **Examples:** `mute loudmouth 30` · `!mute loudmouth`
- **Refuses:** an unknown account; minutes out of range
  (`mute: minutes must be 1..5256000 (ten years); leave it out for a permanent mute`); more words
  (`usage: mute <name> [minutes]`).

## `unmute <name>`

- **Where:** console and chat (`!unmute`).
- **Who:** admin (chat: not for another admin).
- **What:** lifts a mute.
- **Arguments:** `<name>`.
- **Examples:** `unmute loudmouth`
- **Refuses:** an unknown account. An account that is not muted answers `<name> is not muted.`

## `!kick <name>` (chat)

- **Where:** chat only. The console has its own `kick` (below).
- **Who:** admin (not against another admin).
- **What:** revokes the account's login session.
- **Arguments:** `<name>`.
- **Examples:** `!kick griefer`
- **Refuses:** an unknown account; an account with no login session (`<name> has no login session.`).

## `kick <conn>|local|<account>` (console)

- **Where:** console only.
- **Who:** console operator.
- **What:**
  - `kick <conn>`: closes one game connection by its number (1..64, see `players`).
  - `kick local` (or `kick all`): closes all of the local player's game connections.
  - `kick <account>`: revokes that account's login session. Game connections are not per account yet, so use
    `kick local` or `kick <conn>` to close the game itself.
- **Examples:** `kick 2` · `kick local` · `kick griefer`
- **Refuses:** a missing or extra argument (`usage: kick <conn>|local|<account>`); an account with no session
  (`<name> has no login session.`).
