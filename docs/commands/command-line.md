# Command line

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

```
anvil-server [--listen host:port] [--data <dir>] [--simple|--verbose|--quiet] [--no-status]
             [--allow-unbound-launch] [--remote-play]
```

Run with no account flag, `anvil-server` starts the server. The account and offline flags below do one job and exit;
the server does not start. Only one such job flag may be given at a time. An unknown or misplaced flag stops with
`unknown or misplaced argument: <flag>`.

**Who:** whoever can run `anvil-server.exe` on the server PC. These flags act as the console operator.

**Exit codes** of the job flags: `0` means done; any other code means it failed and the printed message says why.
`2` is a bad argument or invalid settings and `3` a refused command (for example an unknown account). The offline
character fixes use `4` when the server is running and `5` when the data folder or store could not be opened.

---

## Running the server

| Flag | What it does | Example |
| --- | --- | --- |
| `--help`, `-h` | prints the flag list and exits | `anvil-server --help` |
| `--listen <host:port>` | listens on this address instead of the `server` key of `anvil-server.cfg` | `anvil-server --listen 0.0.0.0:7500` |
| `--data <dir>` | the data folder (relative to the current folder); also used by the account flags | `anvil-server --data D:\anvil-data` |
| `--simple` | plain operator messages (the default) | `anvil-server --simple` |
| `--verbose` (or `--advanced`) | every event, colour-coded, for debugging | `anvil-server --verbose` |
| `--quiet` | failures only | `anvil-server --quiet` |
| `--no-status` | no live status block; commands are still read line by line | `anvil-server --no-status` |
| `--allow-unbound-launch` | also serves games started without an Anvil Launcher sign-in (development). Their chat commands run as `unbound_chat_account` | `anvil-server --allow-unbound-launch` |
| `--remote-play` | games on other PCs play here, each account in its own world (same as `remote_play = on`) | `anvil-server --remote-play` |

**Refuses:** `--allow-unbound-launch` and `--remote-play` with a job flag
(`--remote-play only applies to running the server`); an invalid `--listen` value (`invalid --listen value`).
A bad `anvil-server.cfg` or `anvil-rules.cfg` stops the server at start and lists the errors.

## Rules file

| Flag | What it does | Example |
| --- | --- | --- |
| `--rules-template` | prints a full `anvil-rules.cfg` with every key commented out at its default, with its range and help, then exits | `anvil-server --rules-template > anvil-rules.cfg` |
| `--rules-check` | checks `anvil-rules.cfg` next to `anvil-server.exe` and lists its errors; the server does not start | `anvil-server --rules-check` |

`--rules-check` prints `anvil-rules.cfg: absent; the server uses the default rates.` or
`anvil-rules.cfg: valid (preset ..., N key(s) set).` with the derived chances, and exits 0. A bad file lists every
error and exits 2. See [Server settings](server-settings.md) for the keys.

## Accounts

These work on the account list in the data folder. Names are 1-32 letters, digits, `_` or `-`; passwords 8-128
characters, asked for and never shown.

| Flag | What it does | Example |
| --- | --- | --- |
| `--list-accounts` | lists accounts with their admin role, bans and mutes | `anvil-server --list-accounts` |
| `--add-account <name>` | creates an account (asks for its password) | `anvil-server --add-account alice` |
| `--set-password <name>` | changes an account's password | `anvil-server --set-password alice` |
| `--remove-account <name> [--delete-data]` | removes an account; keeps its data folder unless `--delete-data` | `anvil-server --remove-account alice --delete-data` |
| `--set-admin <name> on\|off` | grants or removes the admin role | `anvil-server --set-admin alice on` |
| `--ban <name> [minutes] [reason]` | bans an account (permanent without minutes; minutes 1..5256000; reason at most 64 printable ASCII characters). Every word up to the next `--` flag belongs to the ban. A running server signs the account out | `anvil-server --ban griefer 1440 spawn camping` |
| `--unban <name>` | lifts a ban | `anvil-server --unban griefer` |

**Refuses:** an existing name for `--add-account` (`Account <name> already exists (names ignore case).`); an
unknown name for the others (`No account named <name>.`); `--delete-data` without `--remove-account`
(`--delete-data only applies to --remove-account`); the same limits as the console `ban`. There is no `--mute`.

## Offline character fixes (server stopped)

These change the character store directly, so the server must be stopped. They use the character store selected by
the same `ANVIL_*` switches the server runs with. `[slot]` is 1..3; without it, the selected character.

| Flag | What it does | Example |
| --- | --- | --- |
| `--mission-reset [slot]` | `mission reset`: a fresh first-mission start, no grants | `anvil-server --mission-reset 1` |
| `--clear-weapons [slot]` | `clear weapons`: removes the character's weapons except its starter loadout | `anvil-server --clear-weapons` |
| `--eververse-reset [slot]` | `eververse reset`: undoes the Eververse purchases (that character's items, the account's unlocks and the vault) with a refund | `anvil-server --eververse-reset 2` |

**Refuses:** a running server
(`The server is running; stop it first (it keeps the save in memory and would overwrite the change).`, exit 4); a bad
slot; a store that cannot be opened (`The character store could not be opened; nothing was changed.`, exit 5).

## Test sign-in

### `--test-login <host:port> <name> [--fingerprint <hex>]`

- **What:** signs in to a running server's login port (the game port + 1) to test an account. It asks for the
  password. The password is sent only to the expected server: the one whose `--fingerprint` you give (the 64 hex
  digits the server prints), this PC's own server for a `127.x` address, or after you answer `y/N`.
- **Example:** `anvil-server --test-login 127.0.0.1:7501 alice`
- **Refuses:** a missing argument
  (`Use --test-login <host:port> <name> [--fingerprint <hex>] (the login port is the game port + 1).`); an invalid
  account name; a bad fingerprint (`--fingerprint needs the 64 hex digits the server prints.`); `--fingerprint`
  without `--test-login`; an address that cannot be resolved; no password entered.
