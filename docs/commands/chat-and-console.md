# Chat and console: how to type commands

[Back to the index](../README.md)

## Where you can type a command

1. **The server console** (the anvil-server window). Type the command with no prefix, then Enter.
   - Up and Down recall earlier commands (the last 32).
   - Tab completes a command, a subcommand or an account name.
   - The operator at the console can run every command.
2. **In-game chat.** Start the line with `!`, for example `!give glimmer 500`.
   - `/give glimmer 500` also works when Anvil's game DLL is loaded. The DLL sends the `/` lines the game does
     not know as `!` lines and adds the admin commands to the game's chat suggestions. `!` always works.
   - The game has its own `/help`. Anvil's help is `!anvil` (aliases: `!help`, `!?`).
   - Replies are shown only to you.
   - A line is a command only when a letter follows the `!` or `/`. `!!!` or `! hi` is ordinary chat.
3. **The command line** (`anvil-server --...`), for account changes and a few offline fixes.
   See [Command line](command-line.md).
4. **The debug overlay's Server tab** (opt-in: `ANVIL_DEBUG_OVERLAY_244019=1`). It sends one command at a time
   with exactly the same rules as chat. It needs a game signed on to the server.

## Who may use commands

Every account has one of two roles: **player** (the default) or **admin**.

| Role | What happens |
| --- | --- |
| Player | Only `!clan` (see [Clans](players-and-accounts.md#clans)). Every other chat command answers `unknown command`, so admin commands are not revealed. `!anvil` answers `Commands: !clan (your clan). !anvil clan shows its usage.`; `!anvil <other command>` answers `No chat commands are available to your account.` |
| Admin | Runs the chat commands below, and is a server owner for clans (`clan as`, `clan move`). The role is checked again for every command, and a banned admin is treated as a player. |
| Console operator | Runs everything, including the console-only commands. |

Grant the admin role on the console with `accounts admin <name> on`, or from the command line with
`anvil-server --set-admin <name> on`. Chat can never grant or remove the role.

**Which account chat runs as.** Chat commands run as the account the game signed in with through the
Anvil Launcher. A game started without a launcher sign-in (only with `--allow-unbound-launch`) runs as the
`unbound_chat_account` set in `anvil-server.cfg`. That account must still hold the admin role. Without that
setting, such a game has no admin rights.

### What chat admins cannot do

- Grant or remove the admin role (`Only the server console can grant or remove the admin role.`).
- Ban, unban, mute, unmute or kick another admin (`<name> is an admin; only the server console can do that.`).
- Change the rules. `!rules` only shows them.
- Run the console-only commands: `help` (use `!anvil`), `status`, `log`, `players`, `unsupported`, `save` without
  `backup`, `shutdown`, `accounts list|add|remove|passwd`, `rules diff|check|reload`, `scripts reload`, and
  `announce` with no text (the guided form).
- With remote play, an account playing from another PC cannot use `name` or `spawn` from chat.

## Getting help

| Where | Type | You get |
| --- | --- | --- |
| Console | `help` | The command groups (`players`, `items`, `missions`, `progress`, `server`) with what each covers |
| Console | `help <group>` | The commands of one group, for example `help items`, one window at a time |
| Console | `help <command>` | The lines of one command, for example `help mission`. `help give` also lists every item that can be given |
| Chat | `!anvil` | Your chat commands in one line |
| Chat | `!anvil <command>` | The usage of one command, for example `!anvil spawn` |

`!anvil` lists: `admins ban unban mute unmute kick currency characters rank mission give spawn inventory owned
clear eververse pursuit objective save announce event name`.

Some commands work in chat but are left out of that list and of the in-game suggestions (the suggestion list
is full): `!clan`, `!xp`, `!reputation`, `!director show`, `!rules` and `!loot` (an alias of `!inventory`).
`!anvil clan`, `!anvil xp` and `!anvil rules` still show their usage. `!anvil reputation` and `!anvil director` answer
`No such command.`, but the commands themselves run.

**Typos.** An admin who types an unknown command gets suggestions, for example `Did you mean !ban or !unban?`,
or `Type !anvil for the commands.` when nothing is close.

## Limits and safety

- **Failed attempts.** Unknown commands and attempts by non-admins count as failures, per account. After 5
  failures the sender must wait 2 seconds before the next command, doubling up to 60 seconds. The count is
  forgotten after 10 minutes. The reply is `Too many unknown commands; try again in N s.`
- **Busy console.** When the console is running a command, chat answers
  `The server console is running a command; try again in a moment.`
- **Queues.** Character and currency commands are queued for the server. A full queue answers
  `Too many character commands are waiting; try again.` (or the currency version).
- **Log.** Every admin action is written to `anvil-server.log` with who did it (console, command line, chat
  account or overlay). Chat lines themselves are never logged, only the command word, the length and the
  result. Announcement text, ban reasons typed in chat and display names are never logged.

## One player store: "local"

The server keeps one character store, the local player. Where a command takes `<player>`, type `local`.
Any other name is refused: `There is one character store (the local player) until per-account stores exist`.

## Where each command works

"Yes" in the chat column means admins only. See each page for details.

| Command | Console | Chat | Command line | Page |
| --- | --- | --- | --- | --- |
| `help [command]` | Yes | as `!anvil` / `!help` / `!?` | `--help` | this page |
| `status`, `players`, `unsupported` | Yes | No | No | [Server operation](server-operation.md) |
| `log normal\|verbose\|quiet` | Yes | No | `--normal`, `--verbose`, `--quiet` | [Server operation](server-operation.md) |
| `save` | Yes | No | No | [Server operation](server-operation.md) |
| `save backup [label]` | Yes | Yes | No | [Server operation](server-operation.md) |
| `shutdown [seconds]`, `shutdown cancel` | Yes | No | No | [Server operation](server-operation.md) |
| `accounts [list]`, `accounts add`, `accounts remove`, `accounts passwd` | Yes | No | `--list-accounts`, `--add-account`, `--remove-account`, `--set-password` | [Players and accounts](players-and-accounts.md) |
| `accounts admin <name> on\|off` | Yes | No (refused) | `--set-admin` | [Players and accounts](players-and-accounts.md) |
| `admins` | Yes | Yes | No | [Players and accounts](players-and-accounts.md) |
| `characters [local]` | Yes | Yes | No | [Players and accounts](players-and-accounts.md) |
| `name`, `name set <name>` | Yes | Yes | No | [Players and accounts](players-and-accounts.md) |
| `clan ...` | Yes | Yes (every signed-in account; `clan as` and `clan move` admins only) | No | [Players and accounts](players-and-accounts.md#clans) |
| `ban`, `unban` | Yes | Yes | `--ban`, `--unban` | [Moderation](moderation.md) |
| `mute`, `unmute` | Yes | Yes | No | [Moderation](moderation.md) |
| `kick` | Yes (connections or account) | Yes (account session) | No | [Moderation](moderation.md) |
| `give` | Yes | Yes | No | [Items and currency](items-and-currency.md) |
| `currency` | Yes | Yes | No | [Items and currency](items-and-currency.md) |
| `inventory` / `loot` | Yes | Yes | No | [Items and currency](items-and-currency.md) |
| `clear weapons [slot]` | Yes | Yes | `--clear-weapons` (server stopped) | [Items and currency](items-and-currency.md) |
| `eververse reset [slot]` | Yes | Yes | `--eververse-reset` (server stopped) | [Items and currency](items-and-currency.md) |
| `owned clear <vendor> <sale>` | Yes | Yes | No | [Items and currency](items-and-currency.md) |
| `mission reset` | Yes | Yes | `--mission-reset` (server stopped) | [Missions and quests](missions-and-quests.md) |
| `mission checkpoint`, `mission complete` | Yes | Yes | No | [Missions and quests](missions-and-quests.md) |
| `pursuit ...`, `objective set ...` | Yes | Yes | No | [Missions and quests](missions-and-quests.md) |
| `director show [destination]` | Yes | Yes (not listed) | No | [Missions and quests](missions-and-quests.md) |
| `scripts reload [keep]` | Yes | No | No | [Missions and quests](missions-and-quests.md) |
| `rank [1..11]` | Yes | Yes | No | [Progression](progression-xp-ranks.md) |
| `xp` | Yes | Yes (not listed) | No | [Progression](progression-xp-ranks.md) |
| `reputation` | Yes | Yes (not listed) | No | [Progression](progression-xp-ranks.md) |
| `spawn glimmer`, `spawn engram` | Yes | Yes | No | [World and events](world-and-events.md) |
| `event ...` | Yes | Yes | No | [World and events](world-and-events.md) |
| `announce` with text, `list`, `show`, `edit`, `remove`, `clear` | Yes | Yes | No | [World and events](world-and-events.md) |
| `announce` with no text (guided) | Yes | No | No | [World and events](world-and-events.md) |
| `rules` | Yes (full list) | `!rules` (short, read only) | `--rules-check` | [Server settings](server-settings.md) |
| `rules diff`, `rules check`, `rules reload` | Yes | No | No | [Server settings](server-settings.md) |

Most character and store commands need a running server. Without one they answer
`This command needs a running server.` (or `<command> needs a running server.`).
