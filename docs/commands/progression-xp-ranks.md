# Progression: XP and ranks

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

Guardian Rank, character XP with the Rewards Pass, and vendor reputation. All commands need a running server and
work on the server console and in chat (admins). The gameplay rates for XP and reputation are set in
`anvil-rules.cfg` (see [Server settings](server-settings.md)).

---

## `rank [1..11]`

- **Where:** console and chat (`!rank`).
- **Who:** admin.
- **What:** without a number, shows the account's Guardian Rank. With a number, sets it and sends it to a signed-on
  game. With `ANVIL_GRANK_TRACKER_244019=1` the character-select tiles are updated too.
- **Arguments:** `[1..11]`.
- **Examples:** `rank` · `rank 2` · `!rank 5`
- **Refuses:** 0, more than 11, or extra words (`usage: rank [1..11] (no number shows the current Guardian Rank)`).
- **Note:** ranks 3 and above set the rank value only; their record states are not known. Setting the current rank
  answers `Guardian Rank of local is already N.`
- **New accounts:** with `ANVIL_GRANK_TRACKER_244019=1`, a new account starts at Guardian Rank 1. Rank 2 is reached
  by claiming "The Vanguard" in the Guardian Ranks screen, and each later rank by claiming its requirements.
  `rank 1` on an existing account lets the player go through the rank 2 claim again.

## `xp [show]`

- **Where:** console and chat (`!xp`). Chat shows it with `!anvil xp`, but `!anvil` does not list it.
- **Who:** admin.
- **Opt-in:** needs `ANVIL_XP_244019=1`.
- **What:** shows the selected character's Rewards Pass rank and XP, prestige level and XP, character XP, and its XP
  boost percentage (and whether `xp.pass_boosts` is on).
- **Arguments:** none (`show` is optional).
- **Examples:** `xp` · `xp show` · `!xp`
- **Refuses:** the service off (`xp: the XP service is off; start the server with ANVIL_XP_244019=1.`); no
  selected character; no Rewards Pass in force at the server's clock (`xp: no Rewards Pass is in force at this clock.`).

## `xp add <amount>`

- **Where:** console and chat (`!xp add`).
- **Who:** admin.
- **Opt-in:** needs `ANVIL_XP_244019=1`.
- **What:** adds XP to the selected character. The amount is exact: no multiplier and no boost. Pass ranks,
  prestige, Instant rewards and Bright Engrams are given as if the XP were earned, and the change is sent to the
  game. The reply shows the ranks before and after.
- **Arguments:** `<amount>`: 1..100000000. (The Triumphant pass reaches rank 111 at 15,400,000.)
- **Examples:** `xp add 50000` · `!xp add 15400000`
- **Refuses:** 0, over 100000000, or extra words
  (`usage: xp [show] | xp add <amount 1..100000000> (the selected character; ANVIL_XP_244019=1)`); the same
  conditions as `xp show`. Bright Engrams that do not fit (inventory and Postmaster full) are reported in the reply.

## `reputation [show] [<rank>]`

- **Where:** console and chat (`!reputation`). It is not listed by `!anvil` or the chat suggestions.
- **Who:** admin.
- **Opt-in:** needs `ANVIL_VENDOR_RANKS_244019=1`.
- **What:** shows the vendor ranks: level, progress and maximum, rank-up tokens or counter, and resets. With a rank,
  only that one.
- **Arguments:** `[<rank>]`: a vendor rank by name or by progression index. Names: `vanguard`, `crucible`,
  `gambit`, `trials`, `gunsmith`, `xur`, `strange_favor`, `ghost`, `clan_reputation`, `throne_world`, `neomuna`,
  `totality_division`, `the_pikers`, `tharsis_reformation`. At most 32 characters.
- **Examples:** `reputation` · `reputation show vanguard` · `!reputation crucible`
- **Refuses:** the ranks off
  (`reputation: the vendor ranks are off; start the server with ANVIL_VENDOR_RANKS_244019=1.`); no selected
  character; an unknown rank (`reputation: no vendor rank "..." (vanguard, crucible, ...)`).

## `reputation add <rank> <amount>`

- **Where:** console and chat (`!reputation add`).
- **Who:** admin.
- **Opt-in:** needs `ANVIL_VENDOR_RANKS_244019=1`.
- **What:** adds reputation to one vendor rank. The amount is exact (no multiplier). Rank-ups give their token or
  counter and the reward-track rows, as if earned. The change is sent to the game.
- **Arguments:** `<rank>` (as above); `<amount>`: 1..1000000.
- **Examples:** `reputation add vanguard 500` · `!reputation add strange_favor 2000`
- **Refuses:** a missing rank or amount, or an amount out of range (the usage line); an unknown rank; a rank at its
  last step (`reputation add: <rank> is at its last step (N); reset it at the vendor first.`); the same conditions
  as `reputation show`.
