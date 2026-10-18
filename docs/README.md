# Anvil server: command reference

Anvil is a server emulator for the D2 build 244019 game client, played offline or with friends. This reference lists every command the server accepts:
in-game chat commands, server console commands, command-line flags and the settings files.

Start with **[Chat and console](commands/chat-and-console.md)**. It explains where to type commands, who may
use them and how to get help.

## Pages

| Page | What it covers |
| --- | --- |
| [Chat and console](commands/chat-and-console.md) | How to type commands, roles (admin / player), help, the full "where does it work" table |
| [Players and accounts](commands/players-and-accounts.md) | Accounts, the admin role, characters, the display name |
| [Moderation](commands/moderation.md) | Ban, unban, mute, unmute, kick |
| [Items and currency](commands/items-and-currency.md) | Give, currency and materials, inventory, clear weapons, Eververse reset, owned clear |
| [Missions and quests](commands/missions-and-quests.md) | Mission reset and checkpoints, pursuits (quests and bounties), objective values, the Director view, mission scripts |
| [Progression: XP and ranks](commands/progression-xp-ranks.md) | Guardian Rank, XP and the Rewards Pass, vendor reputation |
| [World and events](commands/world-and-events.md) | World pickups (spawn), seasonal events, inbox announcements |
| [Server operation](commands/server-operation.md) | Status, players, save, backups, shutdown |
| [Server settings](commands/server-settings.md) | The `rules` commands, every `anvil-rules.cfg` key, the `anvil-server.cfg` keys |
| [Command line](commands/command-line.md) | Every `anvil-server` flag |

## Conventions used on these pages

- `<name>` is a value you must type. `[name]` is optional. `a|b` means type one of them.
- Command words are not case sensitive. `GIVE glimmer 10` works like `give glimmer 10`.
- "Console" means the anvil-server window. "Chat" means in-game text chat with a `!` prefix.
- "Admin" means an account with the admin role. The operator at the server console can always run everything.
- "The selected character" is the character picked in the game. Most character commands refuse when the game
  is at character select or signed off.
- Commands marked **Opt-in** only work when the server is started with the named switch set to `1`
  (an environment variable, for example `ANVIL_XP_244019=1`).

## Opt-in switches used by commands

| Switch | Needed by |
| --- | --- |
| `ANVIL_XP_244019=1` | `xp`, and the `xp.kill.*`, `xp.activity.*`, `xp.public_event*`, `xp.pass_boosts` rules |
| `ANVIL_VENDOR_RANKS_244019=1` | `reputation`, and the `reputation.portal.*`, `reputation.strike.normal`, `ranks.track.*` rules |
| `ANVIL_OBJECTIVES_244019=1` | `pursuit ...` and `objective set ...` |
| `ANVIL_OBJECTIVES_TOAST_H_244019=1` | the removal notice sent by `pursuit remove` |
| `ANVIL_WORLD_PICKUPS_244019=1` | `spawn glimmer`, `spawn engram` |
| `ANVIL_MISSION_SCRIPT_244019=1` with `ANVIL_MISSION_ENGINE_244019=1` | `scripts reload` |
| `ANVIL_GRANK_TRACKER_244019=1` | `rank` also updates the character-select tiles |
| `ANVIL_DEBUG_OVERLAY_244019=1` | the in-game debug overlay's Server tab (another way to send commands) |
| `ANVIL_CHEST_LOOT_244019=1` | the `chest.*` rules |
| `ANVIL_OBJECTIVES_H_244019=1` | the `xp.pursuit.*` rules (when the XP service is off) |
| `ANVIL_PORTAL_244019=1` | the `power.portal_bonus.*` rules |
| `ANVIL_DISTORTIONS_244019=1` | the `distortions.*` rules (`distortions.matter.chest` also needs `ANVIL_CHEST_LOOT_244019=1`) |
| `ANVIL_SOCKET_COSTS_244019=1` | the `upgrade.cost_multiplier` rule |
| `ANVIL_NET_SHARED_SPACE_244019=1` or `2` | the `shared_space` key of `anvil-server.cfg` (with `multiplayer = fireteam`) |
| `ANVIL_NET_SESSIONS_244019=1` | online session advertisements get a session id (with `multiplayer = fireteam`) |
