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
| `upgrade.cost_multiplier` | multiplier | 0..100 | 1 | Glimmer and materials a socket upgrade costs (masterwork tiers, armor energy, priced mods); 1 is the game data's cost, 0 is free. Only with `ANVIL_SOCKET_COSTS_244019=1` |

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
| `loot.source_pools` | flag | on/off | on | drops follow each item's source in the game data: destination gear drops on its own destination, and faction, destination and Exotic engrams decrypt into their own pools. `off`: one shared pool, as in earlier releases |

#### Power

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `power.floor` | integer | 10..549 | 300 | catch-up: any drop below this comes at it; Collections pulls come at it |
| `power.ordinary_ceiling` | integer | 11..550 | 400 | ordinary sources may climb +1 up to here |
| `power.cap` | integer | 11..550 | 550 | hard cap of any new item (550 is this build's Power cap) |
| `power.climb_chance` | chance | 0..100% | 10% | chance an ordinary drop between the floor and the ceiling climbs (with the data rules: the 0..+1 brackets; left at 10% they use the game data's 50%) |
| `power.climb_amount` | range | each end 1..50 | 1-1 | how much a climb adds when it happens (uniform low-high, so `1-3` = +1, +2 or +3); still capped by `power.ordinary_ceiling` (or, with the data rules, the bracket's top, 400 for ordinary drops) and `power.cap` |
| `power.maintenance_min` | integer | 0..50 | 1 | above the ceiling, ordinary drops come at least this much below current Power |
| `power.maintenance_max` | integer | 0..50 | 5 | ... and at most this much |
| `power.portal_bonus.b` | range | each end 0..50 | 0-0 | Portal end-chest Power above current, grade B |
| `power.portal_bonus.b_plus` | range | each end 0..50 | 0-0 | ... grade B+ |
| `power.portal_bonus.a` | range | each end 0..50 | 2-3 | ... grade A |
| `power.portal_bonus.a_plus` | range | each end 0..50 | 3-4 | ... grade A+ |
| `power.portal_bonus.s` | range | each end 0..50 | 4-5 | ... grade S |
| `power.portal_bonus.s_plus` | range | each end 0..50 | 5-5 | ... grade S+ |

The Portal bonus keys only matter with `ANVIL_PORTAL_244019=1` (the Portal is hosted only with it).

With `ANVIL_POWER_RULES_DATA_244019=1` new gear takes its Power from the game's own reward rules (the same rules the
game uses for its reward previews) and these keys become overrides on top of them:
`power.floor` and `power.cap` are the floor (300) and cap (550) those rules read; `power.climb_chance` and
`power.climb_amount` shape the "chance of +1" brackets of ordinary drops; changing `power.ordinary_ceiling` or
`power.maintenance_min`/`max` switches ordinary drops back to the hand-made rule with your values; changing a
`power.portal_bonus.<grade>` switches that grade's Portal reward back to the hand-made bonus.

#### Chests

Only with `ANVIL_CHEST_LOOT_244019=1`.

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `chest.exotic` | chance | 0..100% | 2% | Exotic engram from an open-world gear chest (a weapon chest gives this share to Legendary) |
| `chest.legendary` | chance | 0..100% | 28% | Legendary from a chest; the rest is Rare |
| `chest.glimmer` | integer | 0..100000 | 0 | Glimmer per chest |
| `chest.lost_sector.list` | flag | on/off | on | a Lost Sector end chest pays one entry of its sector's Expert reward list (Enhancement Cores; solo: the Exotic Engram, a Legendary weapon). `off`: the open-world chest roll (`chest.exotic` / `chest.legendary`) |
| `chest.lost_sector.common` | integer | 0..1000 | 600 | draw weight of a Lost Sector reward-list entry tagged Common (an entry's chance is its weight over the sum of the eligible entries' weights) |
| `chest.lost_sector.uncommon` | integer | 0..1000 | 300 | the same, for an entry tagged Uncommon |
| `chest.lost_sector.rare` | integer | 0..1000 | 100 | the same, for an entry tagged Rare |
| `chest.lost_sector.cores` | integer | 0..99 | 1 | Enhancement Cores paid by a Lost Sector chest's core entry |

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

#### Distortions

Only with `ANVIL_DISTORTIONS_244019=1`. The amounts are server defaults, not values taken from the game data.

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `distortions.matter.kill.minor` | integer | 0..999 | 1 | Strange Matter per minor kill on the Distorted Destination |
| `distortions.matter.kill.elite` | integer | 0..999 | 3 | ... per elite kill |
| `distortions.matter.kill.miniboss` | integer | 0..999 | 5 | ... per miniboss kill |
| `distortions.matter.kill.champion` | integer | 0..999 | 5 | ... per champion kill |
| `distortions.matter.kill.boss` | integer | 0..999 | 10 | ... per boss kill |
| `distortions.matter.kill.vehicle` | integer | 0..999 | 3 | ... per vehicle kill |
| `distortions.matter.chest` | integer | 0..999 | 5 | Strange Matter per chest that pays on the Distorted Destination (also needs `ANVIL_CHEST_LOOT_244019=1`) |
| `distortions.matter.cache` | integer | 0..999 | 10 | Strange Matter a Strange Cache pays after its boss died (on top of its chest roll) |
| `distortions.matter_per_signal` | integer | 1..999 | 50 | Strange Matter that combines into one Strange Signal |
| `distortions.max_signals` | integer | 1..999 | 1 | Strange Signals an account holds at once (Matter keeps adding up to its stack of 999 while full) |

#### Reward tiers

Only with `ANVIL_REWARD_POLICIES_244019=1`; without it these keys are not read and gear keeps the tier of its source
data. They set the gear tier (1..5) of rewards. The numbers are server defaults, not values taken from the game data.
These keys change with `rules reload`.

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `reward.portal.full_package` | flag | on/off | on | a Portal end chest grants the grade's whole package of items. `off`: one item |
| `reward.portal.prime_chance` | chance | 0..100% | 0% | chance a Prime engram reward is one tier higher |
| `reward.event.plus_one` | chance | 0..100% | 15% | public event gear (base Tier 3): chance of Tier 4 |
| `reward.event.plus_two` | chance | 0..100% | 5% | public event gear (base Tier 3): chance of Tier 5. With `reward.event.plus_one` at most 100% |
| `reward.event.heroic_plus_one` | chance | 0..100% | 25% | heroic public event gear (base Tier 4): chance of Tier 5 |
| `reward.event.distortion_bonus` | integer | 0..4 | 1 | tiers a public event's gear gains on a Distorted Destination (`ANVIL_DISTORTIONS_244019=1`), at most Tier 5 |
| `reward.event.items` | integer | 0..5 | 1 | gear items each present member gets at a public event's success or heroic end (`0`: none) |
| `reward.world.tier2` | chance | 0..100% | 30% | open-world gear (kills, chests, Lost Sector chests, untiered engrams): chance of Tier 2; otherwise Tier 1 unless `reward.world.tier3` hits |
| `reward.world.tier3` | chance | 0..100% | 0% | open-world gear: chance of Tier 3. With `reward.world.tier2` at most 100% |
| `reward.dungeon.standard_tier` | integer | 1..5 | 3 | gear of a dungeon that is not the featured one, at Standard difficulty |
| `reward.dungeon.master_tier` | integer | 1..5 | 5 | the same at Master difficulty |
| `reward.dungeon.items` | integer | 0..5 | 1 | gear items each member gets at a dungeon completion (`0`: none) |
| `reward.raid.standard_tier` | integer | 1..5 | 3 | armor of a raid that is not the featured one, at Standard difficulty |
| `reward.raid.master_tier` | integer | 1..5 | 5 | the same at Master difficulty or with challenges |
| `reward.featured.tier` | integer | 1..5 | 5 | gear of the featured raid or dungeon, at any difficulty |
| `reward.featured.dungeons` | list | `none` or a comma list of dungeon names | `throne,pit,prophecy,grasp,duality,spire,ghosts,warlords,vesper,sundered,equilibrium` | the weekly featured-dungeon rotation, in order (up to 12 entries). `none`: no rotation. Names: `throne`, `pit`, `prophecy`, `grasp`, `duality`, `spire`, `ghosts`, `warlords`, `vesper`, `sundered`, `equilibrium` |
| `reward.featured.raids` | list | `none` or a comma list of raid names | `lw,gos,dsc,vog,vow,kf,ron,se,ce,dp` | the weekly featured-raid rotation, in order (up to 12 entries). `none`: no rotation. Names: `lw`, `gos`, `dsc`, `vog`, `vow`, `kf`, `ron`, `se`, `ce`, `dp` |
| `reward.featured.period_weeks` | integer | 1..52 | 1 | weeks each rotation entry stays featured; it changes at the weekly reset (`reset.weekly_day`, `reset.hour_utc`) |
| `reward.featured.offset` | integer | 0..99 | 0 | the rotation entry featured in reset week 1 (the week starting 1970-01-06 17:00 UTC); shifts the whole rotation |
| `reward.featured.dungeon` | pick | `auto`, `none` or a dungeon name | auto | this week's featured dungeon. `auto`: the rotation. `none`: no dungeon is featured |
| `reward.featured.raid` | pick | `auto`, `none` or a raid name | auto | this week's featured raid, the same way |
| `reward.vendor.tier` | integer | 1..5 | 1 | gear bought from a vendor that is neither a destination vendor nor a focusing page |
| `reward.vendor.destination_tier` | integer | 1..5 | 3 | gear bought from a destination vendor |
| `reward.focus.weapon_t4_from` | integer | 300..550 | 400 | a focused weapon is Tier 4 from this current Power (Tier 3 below) |
| `reward.focus.armor_t4_from` | integer | 300..550 | 400 | focused armor is Tier 4 from this current Power (Tier 3 below). At most `reward.focus.armor_t5_from` |
| `reward.focus.armor_t5_from` | integer | 300..550 | 520 | focused armor is Tier 5 from this current Power |
| `reward.collections.tier` | integer | 1..5 | 1 | gear pulled from Collections (its Power stays 300) |
| `reward.exotic_armor_tier` | integer | 0..5 | 5 | Exotic armor from any source except an operator `give` with `tier=`. `0`: the source's own tier |
| `reward.armor.t5_tuning` | flag | on/off | on | a Tier 5 Armor 3.0 drop gets a tuned stat: its tuning socket holds a +5 / -5 tuning mod of a random stat |

#### Vendor purchases

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `vendors.sale_rolls` | flag | on/off | on | a vendor purchase grants the plugs the sale fixes (the perks its preview shows) and one roll per vendor refresh period. `off`: a new random roll per purchase |

#### Vendors and resets (restart keys)

| Key | Type | Range | Default | What it does |
| --- | --- | --- | --- | --- |
| `vendors.special_deliveries_once_per_account` | flag | on/off | on | Special Deliveries rows are claimed once per account and hidden. `off`: claimable again (rows already hidden stay hidden until `owned clear`). [restart] |
| `reset.hour_utc` | integer | 0..23 | 17 | daily and weekly reset hour (UTC). [restart] |
| `reset.weekly_day` | weekday | monday..sunday | tuesday | weekly reset day (weekly free vendor claims, weekly bounties). [restart] |
| `xur.reset_hours` | integer | 0..8760 | 0 | how often Xur's stock re-rolls. `0`: weekly, at the weekly reset (as retail). `24`: daily. `168`: weekly from `xur.reset_day` / `xur.reset_hour`. Any other number: every that many hours. [restart] |
| `xur.reset_day` | weekday | monday..sunday | friday | with `xur.reset_hours` above 0: the weekday of the first Xur reset. [restart] |
| `xur.reset_hour` | integer | 0..23 | 17 | with `xur.reset_hours` above 0: the UTC hour of the Xur reset. [restart] |
| `xur.hoard` | flag | on/off | off | shows Xur's Treasure Hoard in the Director. Side effect: it also switches off the New Light hints and opens the seal pages for characters still in the opening quest. A hoard already shown stays shown after turning it off. [restart] |

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
server (`anvil-server.cfg is invalid ...`), including a bad `multiplayer`, `shared_space`, `relay_ports`, `relay_bind` or
`relay_lan_address`. A value of `remote_play` is `on`/`off` (also `1`/`0`).

| Key | Values | Default | What it does |
| --- | --- | --- | --- |
| `server` | `host:port` (IPv4 or `localhost`, port 1..65535) | `127.0.0.1:7500` | the game port. The login port is port + 1; the local control port is port + 2 (127.0.0.1 only; never forward it). `0.0.0.0:port` listens on every interface |
| `data` | a folder | `server-data` | the data folder; a relative path is relative to `anvil-server.exe` |
| `max_players` | 1..64 | 4 | players signed in at once |
| `log` | `normal`, `verbose`, `quiet` (`simple` and `advanced` are accepted as the old names) | `normal` | the console view the server starts in; `log normal\|verbose\|quiet` switches it while running (`anvil-server.log` always gets every event) |
| `unbound_chat_account` | an account name | unset | chat commands of a game started without a launcher sign-in (`--allow-unbound-launch`) run as this account; it must still be an admin |
| `remote_play` | `on`, `off` | `off` | `on`: games on other PCs play here, each account in its own world; the game port also listens on the `server` host |
| `public_endpoint` | `<IPv4>:<port>` | unset | with `remote_play`: the game address other PCs reach (the router's public address and forwarded game port). The login replies and activity host replies of remote accounts carry it. Unset: login replies name the server host |
| `multiplayer` | `off`, `social`, `fireteam` | `off` | with `remote_play` only; without it the server logs that multiplayer is off. `social`: every account signed in here is a friend of the others (friends list, presence, membership lookup, public profiles) and chat is relayed between them. `fireteam`: `social` plus invites, the fireteam they form, the secure address book, the UDP relay (with `relay_ports`) and joining a fireteam member's activity. `off`: every reply as without multiplayer |
| `shared_space` | `fireteam`, `social_all` | `fireteam` | only has an effect with `ANVIL_NET_SHARED_SPACE_244019` set to `1` or `2`, `multiplayer = fireteam` and `remote_play = on` (otherwise the server logs that the switch is off). Shared spaces are social destinations only. `fireteam`: only a fireteam shares a social space, hosted by its leader. `social_all`: every signed-in player at the same social destination shares one instance; the first to arrive hosts it, and a fireteam lands in its leader's instance (no host migration). Every other activity (missions, strikes, patrol, the shooting range) is private to a fireteam whatever this key says: the leader's launch opens it, only the leader's fireteam joins, and a member who launches alone plays solo. The switch value `1` or `2` selects whose key a guest's player component names as its owner (`1` the host, `2` the guest itself) |
| `relay_ports` | `<first>-<last>` (2..64 ports, 1024..65535, first below last) | unset (no relay) | with `multiplayer = fireteam` and `remote_play = on`: the UDP ports of the fireteam relay (two per relay session, so up to 32 sessions). Forward them (UDP) next to the TCP game and login ports; never forward the control port. Set without `fireteam`, the server logs that the relay is off; if a port cannot be bound, fireteams run without a relay |
| `relay_bind` | `<IPv4>` | `0.0.0.0` | the relay's local address. Used only with a relay |
| `relay_lan_address` | `<IPv4>` (not `0.0.0.0`) | unset | the server's LAN address given to relay clients on a private network (unset: the address their game connection reached; other clients get `public_endpoint`) |
| `patch_notes` | `on`, `off` | `on` | `on`: each newly published release's patch notes (`client-release-notes.md` next to `anvil-server.exe`) are posted once to the in-game inbox as `Anvil Update vX.Y.Z` (see [Inbox announcements](world-and-events.md#automatic-patch-notes)). `off`: never |
| `patch_notes_popup` | `on`, `off` | `off` | `on`: a player who has not seen the newest patch notes gets them as a popup at character select (up to 3 pages, once per account and release). A new account first gets a one-page welcome, once. Needs `patch_notes = on`. Off by default until it has been seen working in game |
| `legacy_titles` | `unlock`, `off` | `unlock` | `unlock`: the seals under Legacy Titles (titles that can no longer be earned) are completed for every account when it signs in, so their titles can be equipped. Their Triumphs count as completed, but their rewards and gilding are not granted. Real progress is never lowered. `off`: nothing is changed (turning it off later removes nothing) |

### Multiplayer settings in short

Multiplayer needs `remote_play = on` and, for fireteams, `multiplayer = fireteam`. A setup for friends on other PCs:

```
remote_play = on
public_endpoint = <public IPv4>:7500
multiplayer = fireteam
relay_ports = 7510-7541
```

Forward TCP game port, TCP login port (game port + 1) and the UDP relay ports. Shared social spaces and session
advertisements are separate opt-in switches (`ANVIL_NET_SHARED_SPACE_244019`, `ANVIL_NET_SESSIONS_244019`); wider
multiplayer is still in development.
