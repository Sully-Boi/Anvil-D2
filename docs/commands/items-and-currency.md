# Items and currency

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

All commands on this page work on the server console and in chat (admins). They need a running server.
`clear weapons` and `eververse reset` also have command-line forms for a stopped server
(see [Command line](command-line.md)). World pickups (`spawn`) are on [World and events](world-and-events.md).

**Slots.** Where a command takes `[slot]`, it is the character slot 1, 2 or 3. Without it, the command uses the
character selected in the game.

**Item hashes** are typed in decimal (`1619016919`) or hexadecimal with `0x` (`0x60803CD7`).

---

## `give <item> [count]`

- **Where:** console and chat (`!give`).
- **Who:** admin.
- **What:** gives wallet currency or a material to the account, a mission weapon to the selected character, or
  any catalog item (a rolled copy) to the selected character. Type `help give` (console) or `!anvil give` for the
  full item list.
- **Arguments:**
  - `<item>`: one of:
    - a wallet currency: `glimmer`, `chronologs`, `silver`, `brightdust` (also `dust`, `bright_dust`,
      `bright-dust`), or their item hashes 3159615086, 4041218086, 3147280338, 2817410917. The new total
      may not pass the maximum stack (Glimmer 500,000; the others 999,999).
    - a material, by name, alias or item hash (see the table below). `_` and `-` are optional and a plural `s`
      is accepted. The new total may not pass the material's maximum stack.
    - a mission weapon, granted once to a fresh-tutorial character:
      - `khvostov` (also `first-weapon`, `firstweapon`, `first_weapon`, `rifle`, 1619016919, `0x60803CD7`):
        the opening mission's first rifle;
      - `stubborn-oak` (also `stubbornoak`, `stubborn_oak`, `shotgun`, `chest-weapon`, 1977926913,
        `0x75E4C501`): the Breach loot cache's shotgun;
      - `butler` (also `butler-rs2`, `butler_rs2`, `rocket`, `rocket-launcher`, `heavy-weapon`,
        `graveyard-weapon`, 2037589099, `0x7973246B`): the Graveyard cache's rocket launcher.
    - `<item hash>`: any catalog item (weapons, armor, ghosts, vehicles, ships, engrams) the selected character's
      class can use. It goes into its bucket, else the Postmaster.
  - `[count]`: 1..9999999, for currencies and materials only. Weapons and catalog items are always count 1.
- **Examples:** `give glimmer 10000` · `!give strange_coin 50` · `give khvostov` · `give 0x60803CD7`
- **Refuses:**
  - an unknown item (`give: unknown item; type help give for the items that can be given`);
  - a count over the maximum stack (`give 900000 Glimmer refused: the maximum is 500000 ...; nothing was changed.`);
  - a count on a weapon or item (`give: weapons are granted once (count 1)`, `give: items are given one at a time (count 1)`);
  - no selected character for a weapon or item;
  - an item for another class (`... is for another class than slot N ...`);
  - a mission weapon for a character on the diagnostic loadout
    (`only fresh-tutorial characters can hold weapon grants`); a weapon the character already has answers
    `Slot N (...) already has <weapon>.`;
  - a full bucket and Postmaster (`... has no room for <item>: its bucket and the Postmaster are full.`).

### Materials `give` and `currency` accept

| Group | Name (alias) and maximum stack |
| --- | --- |
| Materials | `strange_coin` (`coin`) 777, `enhancement_core` (`core`) 9999, `enhancement_prism` (`prism`) 200, `ascendant_shard` 50, `ascendant_alloy` (`alloy`) 50, `upgrade_module` (`module`) 99, `legendary_mark` (`mark`) 9999, `spoils_of_conquest` (`spoils`) 240, `herealways_piece` (`herealways`) 9999, `dark_fragment` 200, `raid_banner` (`banner`) 999 |
| Ciphers | `exotic_cipher` 5, `vanguard_cipher` 50, `crucible_cipher` 50, `iron_cipher` 50, `forsaken_cipher` 3 |
| Event materials | `combat_telemetry` (`telemetry`) 999, `combat_telemetry_b` 999, `combat_telemetry_c` 500, `phantasmal_fragment` 999, `phantasmal_core` 999, `lost_ghost_trace` (`ghost_trace`) 999, `confectionery_heart` (`heart`) 9999, `candy` 99999, `shipwright_plans` 99, `transport_schema` 99, `dawning_spirit` (`spirit`) 9999 |
| Faction engrams | `vanguard_engram`, `crucible_engram`, `gambit_engram`, `trials_engram`, `iron_engram`, `gunsmith_engram`, `pale_heart_engram` (99 each) |

## `give <item hash> [tier=N] [power=N] [perk<N>=<plug hash>]... [force]`

- **Where:** console and chat (`!give`).
- **Who:** admin.
- **What:** gives one catalog item with a chosen roll. Options go in any order, each at most once. What you do not
  choose rolls as usual.
- **Arguments:**
  - `tier=1..5`: the gear tier, for gear-tier weapons and armor only. As with drops, Tier 2+ enhances the trait
    columns, Tier 4+ barrels and magazines, Tier 5 the origin trait.
  - `power=10..550`: the item's Power (550 is this build's Power cap).
  - `perk<N>=<plug hash>`: the plug for socket N (0..15, the item's socket index). Plugs are given by hash only
    (decimal or `0x`); there are no plug names. The plug must be in that socket's plug set. It is enhanced when the
    tier enhances its column; an enhanced plug's hash means its base plug.
  - `force`: lets a `perk<N>=` name a plug outside the socket's plug set, if its plug category is one the socket
    takes (for example a trait from the other trait column). Needs at least one `perk<N>=`.
- **Examples:** `give 189854537 tier=5 power=550` ·
  `give 189854537 tier=5 power=550 perk3=2077819806 perk4=2077819806 force`
- **Refuses:**
  - roll options on a currency, material or mission weapon
    (`give: tier=, power=, perk<N>= and force are for catalog items given by hash`);
  - an option twice, an unknown option, or a value out of range (`give: tier= is a gear tier 1..5`,
    `give: power= is 10..550 ...`, `give: perk<N>= takes a socket index N of 0..15 ...`);
  - `force` alone (`give: force goes with a perk<N>=`);
  - an unknown plug hash; `tier=` on an item with no gear tier; `power=` on an item with no Power;
  - a socket with no random plug set, an armor archetype or stat socket (rolled as a set), a socket never filled on
    this item, a plug outside the plug set without `force`, or a plug of a category the socket does not take.

## `currency local list`

- **Where:** console and chat (`!currency local list`).
- **Who:** admin.
- **What:** shows the wallet (Glimmer, Chronologs, Silver, Bright Dust) and the materials held.
- **Arguments:** `local` (the only player store).
- **Examples:** `currency local list`
- **Refuses:** any player other than `local`.

## `currency local set|add|remove <currency> <amount>`

- **Where:** console and chat (`!currency`).
- **Who:** admin.
- **What:** changes one balance. The result must stay within 0 and the maximum stack; nothing is clamped. The change
  is sent to a signed-on game.
- **Arguments:**
  - `set|add|remove`.
  - `<currency>`: `glimmer`, `chronologs`, `silver`, `brightdust`, or a material (table above).
  - `<amount>`: a whole number up to 9999999. `set` accepts 0; `add` and `remove` need at least 1.
- **Examples:** `currency local set glimmer 250000` · `!currency local add enhancement_core 100` ·
  `currency local remove silver 500`
- **Refuses:** an amount past the maximum (`... refused: the maximum is N (local has M); nothing was changed.`);
  removing more than held (`remove N <currency> refused: local has only M; nothing was changed.`); an unknown
  currency (`currency: glimmer, chronologs, silver, brightdust or a material (help give lists them)`); a material
  whose account bucket has no free row.

## `inventory [slot] [page]` (alias `loot`)

- **Where:** console and chat (`!inventory`, `!loot`).
- **Who:** admin.
- **What:** lists a character's owned items (mission weapons, kill drops and gifts), 8 per page.
- **Arguments:** `[slot]`: 1..3; `[page]`: 1..99. To pick a page you must also give the slot.
- **Examples:** `inventory` · `inventory 2` · `!loot 1 3`
- **Refuses:** a bad slot or page (`usage: inventory [slot 1..3] [page] (no slot: the selected character)`); an
  empty slot (`No character in slot N (type characters).`); no selected character; a page past the end
  (`Slot N has M page(s) of items.`).

## `clear weapons [slot]`

- **Where:** console, chat (`!clear weapons`) and command line (`--clear-weapons [slot]`, server stopped).
- **Who:** admin.
- **What:** removes every weapon of the character (equipped, in the bucket and in the Postmaster) except its starter
  loadout. It applies on the next character load.
- **Arguments:** `[slot]`: 1..3.
- **Examples:** `clear weapons` · `!clear weapons 2`
- **Refuses:** a bad slot (`clear weapons: the character slot is 1, 2 or 3`); an empty slot; no selected character.
  A character with nothing to remove answers `... has no weapons to remove (starter loadout kept).`

## `eververse reset [slot]`

- **Where:** console, chat (`!eververse reset`) and command line (`--eververse-reset [slot]`, server stopped).
- **Who:** admin.
- **What:** undoes the account's Eververse purchases. Their items go (from the chosen character and the vault),
  their unlocks and Collections flags go, equipped ones fall back to defaults, and the price is refunded. Items
  another character still holds are kept until their last copy goes.
- **Arguments:** `[slot]`: 1..3.
- **Examples:** `eververse reset` · `!eververse reset 1`
- **Refuses:** a bad slot (`eververse reset: the character slot is 1, 2 or 3`); an empty slot; no selected
  character. With nothing to undo it answers `no Eververse purchases to undo`.

## `owned clear <vendor> <sale>`

- **Where:** console and chat (`!owned clear`).
- **Who:** admin.
- **What:** clears a vendor sale's "owned" flag so it can be bought again. Items already received stay. For a free
  material sale it releases its once-only claim; for a Special Deliveries row it clears the claim so the row shows
  again.
- **Arguments:** `<vendor>` and `<sale>`: the purchase's vendor and sale index, each 0..65535.
- **Examples:** `owned clear 1184 15` (the Eagle's Pack)
- **Refuses:** a bad form (`usage: owned clear <vendor> <sale> ...`); an unknown sale (`No sale S at vendor V ...`);
  a sale with no collectible flag (`... has no collectible flag; nothing to clear.`). A sale that is not owned, or a
  free sale not yet claimed, answers that nothing was changed.
