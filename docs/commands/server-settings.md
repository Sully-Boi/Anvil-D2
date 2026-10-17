# Server settings

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

Two text files next to `anvil-server.exe` configure the server:

- **`anvil-rules.cfg`**: the gameplay rates (loot, Power, XP, reputation, resets). Optional.
- **`anvil-server.cfg`**: the network address, data folder and multiplayer options.

---

## The `rules` commands

### `rules`

- **Where:** console only (chat has the short `!rules`, below). **Who:** console operator.
- **What:** read only. Shows the preset, every key with its value and where it came from (`anvil-rules.cfg:<line>`,
  `preset <name>` or `default`, and `[restart]` for restart keys), then the derived chances (the chance a kill drops
  an item, per enemy tier, and the chest odds), then the state of the switches the rates depend on
  (`ANVIL_CHEST_LOOT_244019`, `ANVIL_OBJECTIVES_H_244019`, `ANVIL_PORTAL_244019`).
- **Examples:** `rules`

### `rules diff`

- **Where:** console only. **Who:** console operator.
- **What:** like `rules`, but only the keys that differ from retail. With none it says
  `(every key at its retail value)`.
- **Examples:** `rules diff`

### `rules check`

- **Where:** console only. **Who:** console operator.
- **What:** reads `anvil-rules.cfg` now and lists its errors. It changes nothing. A good file answers
  `anvil-rules.cfg: valid (preset ..., N key(s)); rules reload applies it`. No file answers
  `anvil-rules.cfg: absent (the defaults apply)`.
- **Examples:** `rules check`

### `rules reload`

- **Where:** console only. **Who:** console operator.
- **What:** reads `anvil-rules.cfg` again. The rates change at once and each change is logged. The restart keys
  (`reset.*` and `vendors.*`) keep their running value until the next start; the log says
  `<key> stays <value> until a restart`. With no file, the defaults come back.
- **Examples:** `rules reload`
- **Refuses:** a file with errors or that cannot be read: nothing changes and the errors go to the log
  (`reload refused: anvil-rules.cfg has errors; the rules in force stay`).

All four refuse other words: `usage: rules [diff|check|reload]`.

### `!rules` (chat)

- **Where:** chat only. **Who:** admin.
- **What:** read only. Shows the preset, how many keys differ from retail, up to 12 of those keys
  (`key = value`, then `... more on the server console (rules diff).`), and the kill and chest chances.
- **Examples:** `!rules`
- **Refuses:** any extra word (`usage: !rules (read only; rules reload is on the server console)`); non-admins
  get `unknown command`.

On the command line, `anvil-server --rules-check` checks the file and `--rules-template` prints every key
(see [Command line](command-line.md)).

---

## `anvil-rules.cfg`

### How the file works

- Put it next to `anvil-server.exe`. An absent or empty file means the retail defaults.
- One `key = value` per line. `#` starts a comment (also after a value). Delete or comment out a line to get its
  default back. Keys are lower case.
- `preset = retail | casual | hardcore` is applied first, then every other key, in any line order.
- **Value formats:**
  - **chance**: a fraction (`0.013`) or a percent (`1.3%`), in 0.1 % steps, 0 % to 100 %;
  - **multiplier**: `2`, `1.5`, `x0.25` or `0.25x`, in 0.01 steps, 0 to 100;
  - **flag**: `on`/`off` (also `true`/`false`, `1`/`0`);
  - **range**: `low-high` (`2-3`), or one number for both ends;
  - **rarity**: `common` (no floor), `uncommon`, `rare`, `legendary`;
  - **weekday**: `monday` .. `sunday`;
  - **integer**: whole numbers only.
- **Errors.** A bad file stops the server at start and lists every error, each as
  `anvil-rules.cfg:<line>: ...`. Errors are: a line that is not `key = value`; a key with no value; an unknown key
  (with a "did you mean"); a key or `preset` set twice; a malformed value; a value out of range; a chance finer than
  0.1 % or a multiplier finer than 0.01; a file over 64 KiB. Cross-key checks:
  `power.floor < power.ordinary_ceiling <= power.cap`, `power.maintenance_min <= power.maintenance_max`, and
  `chest.exotic + chest.legendary` at most 100 %.
- **Restart keys** (marked [restart]) are read at start only. Every other key changes with `rules reload`.
- **Multipliers** only scale gameplay sources. The Glimmer, currency, materials and reputation multipliers do not
  touch vendors, Eververse, record (Triumph) rewards or admin commands. `xp add` and `reputation add` are exact.

### Presets

`retail` is the default (every key at the values below). `casual` and `hardcore` change only these keys:

| Key | retail | casual | hardcore |
| --- | --- | --- | --- |
| `loot.drop_multiplier` | 1 | 2 | 0.5 |
| `glimmer.multiplier` | 1 | 2 | 0.5 |
| `currency.multiplier` | 1 | 2 | 0.75 |
| `materials.multiplier` | 1 | 2 | 0.75 |
| `xp.multiplier` | 1 | 2 | 0.75 |
| `reputation.multiplier` | 1 | 2 | 1 |
| `power.climb_chance` | 10% | 25% | 5% |
| `chest.exotic` | 2% | 4% | 1% |
| `chest.legendary` | 28% | 40% | 20% |

`loot.drop_multiplier` also scales the chest odds, so `rules` shows the effective chest odds
(for `casual`: Exotic 8 %, Legendary 80 %).

### Every key

#### Multipliers

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `loot.drop_multiplier` | multiplier | 0..100 | 1 | every kill and chest item chance (each at most 100 %) |
| `glimmer.multiplier` | multiplier | 0..100 | 1 | Glimmer from kills, chests, dismantling and quest/bounty/mission rewards |
| `currency.multiplier` | multiplier | 0..100 | 1 | Bright Dust, Silver and Chronologs from quest, bounty and mission rewards |
| `materials.multiplier` | multiplier | 0..100 | 1 | materials from quest, bounty and mission rewards (Enhancement Cores, Strange Coins...) |
| `xp.multiplier` | multiplier | 0..100 | 1 | every XP grant |
| `reputation.multiplier` | multiplier | 0..100 | 1 | reputation from quest, bounty and mission rewards (Strange Favor, Clan, Throne World, Neomuna) |

#### Kill drops

`<tier>` is `minor`, `elite`, `miniboss`, `champion`, `boss` or `vehicle`. Each kill makes three independent item
draws; the rarest result is kept.

| Key | Type | Range | What it does |
| --- | --- | --- | --- |
| `loot.kill.<tier>.glimmer` | integer | 0..10000 | Glimmer per kill |
| `loot.kill.<tier>.rare` | chance | 0..100% | chance of a leveling-pool item (Rare or better from `loot.min_rarity_from_power`) |
| `loot.kill.<tier>.legendary` | chance | 0..100% | chance of a Legendary engram (decrypts on pickup) |
| `loot.kill.<tier>.exotic` | chance | 0..100% | chance of an Exotic engram |

Defaults:

| Tier | glimmer | rare | legendary | exotic |
| --- | --- | --- | --- | --- |
| minor | 5 | 1% | 0.3% | 0% |
| elite | 25 | 5% | 1.7% | 0% |
| miniboss | 50 | 20% | 6.7% | 0.1% |
| champion | 75 | 20% | 6.7% | 0.1% |
| boss | 100 | 20% | 6.7% | 0.2% |
| vehicle | 50 | 5% | 1.7% | 0% |

#### Loot floor and social spaces

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `loot.min_rarity` | rarity | common..legendary | rare | lowest rarity of random gear at or above `loot.min_rarity_from_power` (`common` = no floor) |
| `loot.min_rarity_from_power` | integer | 10..550 | 300 | current Power from which `loot.min_rarity` applies |
| `loot.social_space_drops` | flag | on/off | off | world drops in social and practice spaces (Tower, Lighthouse, Shooting Range, Xur's hoard) |

#### Power

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `power.floor` | integer | 10..549 | 300 | catch-up: any drop below this comes at it; Collections pulls come at it |
| `power.ordinary_ceiling` | integer | 11..550 | 400 | ordinary sources may climb +1 up to here |
| `power.cap` | integer | 11..550 | 550 | hard cap of any new item (550 is this build's Power cap) |
| `power.climb_chance` | chance | 0..100% | 10% | chance an ordinary drop between the floor and the ceiling is +1 |
| `power.maintenance_min` | integer | 0..50 | 1 | above the ceiling, ordinary drops come at least this much below current Power |
| `power.maintenance_max` | integer | 0..50 | 5 | ... and at most this much |
| `power.portal_bonus.b` | range | each end 0..50 | 0-0 | Portal end-chest Power above current, grade B |
| `power.portal_bonus.b_plus` | range | each end 0..50 | 0-0 | ... grade B+ |
| `power.portal_bonus.a` | range | each end 0..50 | 2-3 | ... grade A |
| `power.portal_bonus.a_plus` | range | each end 0..50 | 3-4 | ... grade A+ |
| `power.portal_bonus.s` | range | each end 0..50 | 4-5 | ... grade S |
| `power.portal_bonus.s_plus` | range | each end 0..50 | 5-5 | ... grade S+ |

The Portal bonus keys only matter with `ANVIL_PORTAL_244019=1` (the Portal is hosted only with it).

#### Chests

Only with `ANVIL_CHEST_LOOT_244019=1`.

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `chest.exotic` | chance | 0..100% | 2% | Exotic engram from an open-world gear chest (a weapon chest gives this share to Legendary) |
| `chest.legendary` | chance | 0..100% | 28% | Legendary from a chest; the rest is Rare |
| `chest.glimmer` | integer | 0..100000 | 0 | Glimmer per chest |

#### Dismantling

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `dismantle.glimmer.common` | integer | 0..100000 | 0 | Glimmer for dismantling a Common (white) item |
| `dismantle.glimmer.uncommon` | integer | 0..100000 | 10 | ... an Uncommon (green) item |
| `dismantle.glimmer.rare` | integer | 0..100000 | 25 | ... a Rare (blue) item |
| `dismantle.glimmer.legendary` | integer | 0..100000 | 50 | ... a Legendary item |
| `dismantle.glimmer.exotic` | integer | 0..100000 | 100 | ... an Exotic item |

#### XP

The pursuit XP keys apply with the XP service (`ANVIL_XP_244019=1`) or with `ANVIL_OBJECTIVES_H_244019=1`.
Every other XP key needs `ANVIL_XP_244019=1`.

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `xp.pursuit.xp` | integer | 0..1000000 | 5000 | XP of a pursuit reward "XP" |
| `xp.pursuit.xp_plus` | integer | 0..1000000 | 10000 | ... "XP+" |
| `xp.pursuit.xp_plus_plus` | integer | 0..1000000 | 25000 | ... "XP++" |
| `xp.pursuit.double` | integer | 0..1000000 | 50000 | ... "Double XP" |
| `xp.kill.minor` | integer | 0..1000000 | 50 | XP per minor kill |
| `xp.kill.elite` | integer | 0..1000000 | 250 | ... elite kill |
| `xp.kill.miniboss` | integer | 0..1000000 | 1000 | ... miniboss kill |
| `xp.kill.champion` | integer | 0..1000000 | 1500 | ... champion kill |
| `xp.kill.boss` | integer | 0..1000000 | 5000 | ... boss kill |
| `xp.kill.vehicle` | integer | 0..1000000 | 250 | ... vehicle kill |
| `xp.activity.story` | integer | 0..10000000 | 20000 | XP for completing a story mission |
| `xp.activity.strike` | integer | 0..10000000 | 20000 | ... a strike |
| `xp.activity.lost_sector` | integer | 0..10000000 | 8000 | ... a Lost Sector (no effect yet: no Lost Sector is classified) |
| `xp.activity.patrol` | integer | 0..10000000 | 2000 | ... a patrol activity |
| `xp.activity.portal` | integer | 0..10000000 | 25000 | ... a Portal activity |
| `xp.public_event` | integer | 0..10000000 | 10000 | XP for a public event (no effect yet: completions are not detected) |
| `xp.public_event.heroic` | integer | 0..10000000 | 5000 | ... extra for a heroic public event |
| `xp.pass_boosts` | flag | on/off | on | the Rewards Pass XP boosts (Large 20 %, Small 4 %, Minor 3 %, added together) once granted |

#### Reputation and vendor ranks

Only with `ANVIL_VENDOR_RANKS_244019=1`.

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `reputation.portal.normal` | integer | 0..100000 | 100 | Vanguard Rank per Portal Ops completion at Normal |
| `reputation.portal.advanced` | integer | 0..100000 | 125 | ... at Advanced |
| `reputation.portal.expert` | integer | 0..100000 | 150 | ... at Expert |
| `reputation.portal.master` | integer | 0..100000 | 200 | ... at Master |
| `reputation.portal.grandmaster` | integer | 0..100000 | 250 | ... at Grandmaster |
| `reputation.portal.ultimate` | integer | 0..100000 | 300 | ... at Ultimate |
| `reputation.strike.normal` | integer | 0..100000 | 100 | Vanguard Rank per strike completion (strikes are always Normal) |
| `ranks.track.1` | integer | 1..16 | 4 | the vendor rank (level) that earns reward-track row 1 |
| `ranks.track.2` | integer | 1..16 | 7 | ... row 2 |
| `ranks.track.3` | integer | 1..16 | 10 | ... row 3 |
| `ranks.track.4` | integer | 1..16 | 13 | ... row 4 |
| `ranks.track.5` | integer | 1..16 | 16 | ... row 5 |

#### Vendors and resets (restart keys)

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `vendors.special_deliveries_once_per_account` | flag | on/off | on | Special Deliveries rows are claimed once per account and hidden. `off`: claimable again (rows already hidden stay hidden until `owned clear`). [restart] |
| `reset.hour_utc` | integer | 0..23 | 17 | daily and weekly reset hour (UTC). [restart] |
| `reset.weekly_day` | weekday | monday..sunday | tuesday | weekly reset day (weekly free vendor claims, weekly bounties). [restart] |

### Example file

```
# anvil-rules.cfg
preset = retail
loot.drop_multiplier = 1.5      # every kill and chest item chance x1.5
loot.kill.boss.exotic = 0.5%    # boss Exotic engram chance (default 0.2 %)
glimmer.multiplier = 2
xp.multiplier = 1.5
# reset.weekly_day = tuesday    # restart key
```

---

## `anvil-server.cfg`

Same line rules: `key = value`, `#` comments. Unknown keys are ignored. A malformed value of a known key stops the
server (`anvil-server.cfg is invalid ...`).

| Key | Values | Default | What it does |
| --- | --- | --- | --- |
| `server` | `host:port` (IPv4 or `localhost`, port 1..65535) | `127.0.0.1:7500` | the game port. The login port is port + 1; the local control port is port + 2 (127.0.0.1 only; never forward it). `0.0.0.0:port` listens on every interface |
| `data` | a folder | `server-data` | the data folder; a relative path is relative to `anvil-server.exe` |
| `max_players` | 1..64 | 4 | players signed in at once |
| `log` | `simple`, `advanced`, `quiet` | `simple` | console message level (`anvil-server.log` always gets every event) |
| `unbound_chat_account` | an account name | unset | chat commands of a game started without a launcher sign-in (`--allow-unbound-launch`) run as this account; it must still be an admin |
| `remote_play` | `on`, `off` | `off` | `on`: games on other PCs play here, each account in its own world; the game port also listens on the `server` host |
| `public_endpoint` | `<IPv4>:<port>` | unset | with `remote_play`: the game address other PCs reach (the router's public address and forwarded game port) |
| `multiplayer` | `off`, `social`, `fireteam` | `off` | with `remote_play` only. `social`: every account here is a friend of the others (friends list, presence, profiles) and chat is relayed. `fireteam`: `social` plus invites, the fireteam, the UDP relay and joining a member's activity |
| `relay_ports` | `<first>-<last>` (2..64 ports, 1024..65535) | unset (no relay) | with `multiplayer = fireteam`: the UDP relay ports (two per relay session). Forward them (UDP) with the game and login ports |
| `relay_bind` | `<IPv4>` | `0.0.0.0` | the relay's local address |
| `relay_lan_address` | `<IPv4>` | unset | the server's LAN address given to relay clients on a private network |
| `patch_notes` | `on`, `off` | `on` | `on`: each newly published release's patch notes (`client-release-notes.md` next to `anvil-server.exe`) are posted once to the in-game inbox as `Anvil Update vX.Y.Z` (see [Inbox announcements](world-and-events.md#automatic-patch-notes)). `off`: never |
| `patch_notes_popup` | `on`, `off` | `off` | `on`: a player who has not seen the newest patch notes gets them as a popup at character select (up to 3 pages, once per account and release). Needs `patch_notes = on`. Off by default until it has been seen working in game |
