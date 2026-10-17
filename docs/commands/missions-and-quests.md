# Missions and quests

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

Commands for the first mission (A Guardian Rises), pursuits (quest steps and bounties), objective values,
the Director view and mission scripts. All need a running server, except `mission reset` from the command line.

**Slots.** `[slot]` is the character slot 1, 2 or 3. Without it the command uses the character selected in the
game.

**Item hashes and IDs** are typed in decimal or hexadecimal with `0x`.

---

## Mission

### `mission reset [slot] [opening|exterior]`

- **Where:** console, chat (`!mission reset`) and command line (`--mission-reset [slot]`, server stopped).
- **Who:** admin.
- **What:** gives the character a fresh first-mission start with no weapon grants. `opening` (the default) starts at
  the Opening: its cinematic plays on a server set up for the opening hand-off, otherwise the Opening checkpoint
  starts in the Exterior. `exterior` starts at the Exterior. It applies on the next activity load.
- **Arguments:** `[slot]`: 1..3. `[opening|exterior]`: may follow the slot or stand alone.
- **Examples:** `mission reset` · `mission reset 2` · `!mission reset exterior` · `mission reset 1 exterior`
- **Refuses:** a bad slot (`mission reset: the character slot is 1, 2 or 3`); another start word
  (`mission reset: the start is opening or exterior`); an empty slot (`No character in slot N (type characters).`);
  no selected character. A character already at a fresh start answers `... is already at a fresh mission start`.

### `mission checkpoint <area>`

- **Where:** console and chat (`!mission checkpoint`).
- **Who:** admin.
- **What:** sets the selected character's first-mission checkpoint. It applies on the next activity load.
- **Arguments:** `<area>`: `0`..`5`, or a name: `opening` (0), `exterior` (1), `breach` (2), `graveyard` (3),
  `transit` (4), `hangar` (5).
- **Examples:** `mission checkpoint graveyard` · `!mission checkpoint 4`
- **Refuses:** an unknown area
  (`mission checkpoint: area 0..5 or opening, exterior, breach, graveyard, transit, hangar`); no selected character.

### `mission complete [slot]`

- **Where:** console and chat (`!mission complete`).
- **Who:** admin.
- **What:** completes A Guardian Rises for the character: the pursuit moves to step 2 and the Director is released.
  The change is sent to a signed-on game. There is no completion screen and no return to orbit.
- **Arguments:** `[slot]`: 1..3.
- **Examples:** `mission complete` · `!mission complete 3`
- **Refuses:** a bad or empty slot; no selected character; a character without the A Guardian Rises pursuit
  (`... has no A Guardian Rises Pursuit to complete; nothing was changed.`). Already done answers
  `... has already completed A Guardian Rises.`

---

## Pursuits (quests and bounties)

**Opt-in:** every `pursuit` command needs `ANVIL_OBJECTIVES_244019=1`. Without it:
`pursuit: the objective publication is off; start the server with ANVIL_OBJECTIVES_244019=1`.
Every command works on the selected character.

### `pursuit give <item hash>`

- **Where:** console and chat (`!pursuit give`).
- **Who:** admin.
- **What:** grants a quest step or a bounty. It appears in the Quests tab at once.
- **Arguments:** `<item hash>`: the quest step's or bounty's item hash.
- **Examples:** `pursuit give 0x3C26A638` · `!pursuit give 0x3C26A638`
- **Refuses:** a malformed hash (`pursuit give: the item hash is decimal or 0x... (a quest step or bounty)`);
  a hash that is not a quest step or bounty of this build; a pursuit that cannot be granted (the reply says why,
  for example when it is already held); no selected character.

### `pursuit list`

- **Where:** console and chat (`!pursuit list`).
- **Who:** admin.
- **What:** lists the character's pursuits: ID, item hash, bucket row, whether it is tracked, its progress values
  (value index = value) and targets.
- **Arguments:** none.
- **Examples:** `pursuit list`
- **Refuses:** no selected character.

### `pursuit remove <ID|item hash>`

- **Where:** console and chat (`!pursuit remove`).
- **Who:** admin.
- **What:** removes a held pursuit. No reward is given. With `ANVIL_OBJECTIVES_TOAST_H_244019=1` the game is also
  sent the removal notice.
- **Arguments:** the pursuit's ID from `pursuit list` (`0x` and up to 16 hex digits) or its item hash.
- **Examples:** `pursuit remove 0x420000000000913E` (an ID from `pursuit list`) · `pursuit remove 0x3C26A638` (by item hash)
- **Refuses:** a malformed ID (`pursuit remove: the pursuit's ID (0x42..., see pursuit list) or its item hash`);
  a pursuit the character does not hold (`pursuit remove: slot N holds no pursuit ...`).

### `pursuit advance <ID|item hash>`

- **Where:** console and chat (`!pursuit advance`).
- **Who:** admin.
- **What:** an operator turn-in. A held quest step (A Guardian Rises too) moves to its next step and gives its
  rewards. A held bounty is turned in for its rewards.
- **Arguments:** the pursuit's ID or item hash.
- **Examples:** `pursuit advance 0x3C26A638` · `pursuit advance 0x420000000000913E`
- **Refuses:** a malformed ID; a pursuit that cannot be advanced (`pursuit advance: ... is not advanced: <why>.
  Nothing was changed.`).

### `pursuit record <row>`

- **Where:** console and chat (`!pursuit record`).
- **Who:** admin.
- **What:** read only. Shows one Triumph (record) row as the server sees it: its state, how many objectives are
  complete, its interval counter or redeemed flag, its score and the total Triumph score.
- **Arguments:** `<row>`: the record row, 0..6168.
- **Examples:** `pursuit record 1307`
- **Refuses:** a row out of range (`pursuit record: the record row, 0..6168`); no selected character.

### `pursuit score`

- **Where:** console and chat (`!pursuit score`).
- **Who:** admin.
- **What:** read only. Shows the account's Triumph score, the records redeemed and the intervals counted.
- **Arguments:** none.
- **Examples:** `pursuit score`
- **Refuses:** no selected character.

---

## Objective values

**Opt-in:** every `objective set` command needs `ANVIL_OBJECTIVES_244019=1`. These commands write raw progress
values. Use them for testing. Changes are sent to a signed-on game.

Common rules: the row or index is a number 0..65535 (the real limit of each bank is checked when the command runs).
The value is a whole number -2147483648..2147483647 unless noted. Rows other features own are refused:
account row 2977 (the Guardian Rank: use `rank`), the values of completed Exotic catalysts, character row 616 and
flag row 149 (both belong to the first mission).

### `objective set account <row> <value>`

- **Where:** console and chat. **Who:** admin.
- **What:** sets an account value row.
- **Examples:** `objective set account 100 40`
- **Refuses:** a row past the bank (`objective set account: the row is 0..N.`); row 2977; a completed catalyst's row.

### `objective set character <row> <value>`

- **Where:** console and chat. **Who:** admin.
- **What:** sets a value row of the selected character.
- **Examples:** `objective set character 5 -1`
- **Refuses:** a row past the bank; row 616; no selected character.

### `objective set flag <row> default|false|true`

- **Where:** console and chat. **Who:** admin.
- **What:** sets the selected character's flag override. `default` removes the override.
- **Arguments:** `default` (or `0`), `false` (or `1`), `true` (or `2`).
- **Examples:** `objective set flag 150 true` · `objective set flag 150 default`
- **Refuses:** another value (`objective set flag: the value is default, false or true`); a row past the bank;
  row 149; no selected character.

### `objective set value <index> <value>`

- **Where:** console and chat. **Who:** admin.
- **What:** sets a value by its definition index. An account value (type 1) or character value (type 2) is set
  directly. A pursuit value (type 4) sets the progress of every held pursuit that lists it, which moves its bar.
  When this completes a quest step, the step advances.
- **Examples:** `objective set value 13686 3`
- **Refuses:** an index that is not a value definition; a value whose bank is not known; a type-4 value no held
  pursuit lists (`... no pursuit held by slot N lists value V (pursuit list shows them).`).

### `objective set progression <index> <value>`

- **Where:** console and chat. **Who:** admin.
- **What:** sets a progression's progress (XP, reputation) on the account or the selected character.
- **Arguments:** `<index>`: the progression index, for example `6` (character XP) or `156` (Vanguard Rank).
  `<value>`: 0..2147483647.
- **Examples:** `objective set progression 156 300` · `objective set progression 6 5000`
- **Refuses:** a negative value (`objective set progression: the progress is 0..2147483647`); an index neither bank
  holds; no selected character for a character progression.

---

## Director view

### `director show [destination]`

- **Where:** console, and chat (`!director show`). It is not listed by `!anvil` or the chat suggestions.
- **Who:** admin.
- **What:** read only. Shows what the Director shows the selected character. Without a destination: every top-level
  link with its visibility and active target (a Campaign version names one activity). With a destination: its map's
  visible nodes, their states and activities. Then the quest markers of the held pursuits.
- **Arguments:** `[destination]`: a word of its name (for example `cosmodrome`, `neomuna`, `throne`, `pale`,
  `tower`) or its link id. At most 32 characters.
- **Examples:** `director show` · `director show neomuna` · `!director show tower`
- **Refuses:** an unknown destination (`director show: no such destination; type director show for the list ...`);
  a destination over 32 characters; no selected character. A very long answer ends with
  `(more: the reply is full)`.

---

## Mission scripts

### `scripts reload [keep]`

- **Where:** console only.
- **Who:** console operator.
- **Opt-in:** needs `ANVIL_MISSION_SCRIPT_244019=1` with `ANVIL_MISSION_ENGINE_244019=1`.
- **What:** recompiles the mission scripts. Each running mission swaps to its new program at a safe point and starts
  a new attempt: once-keys, attempt variables, timers and squad reads reset; variables and activity once-keys are
  kept. A failed compile keeps the old program.
- **Arguments:** `keep`: swap the program but let the attempt go on unchanged.
- **Examples:** `scripts reload` · `scripts reload keep`
- **Refuses:** scripts switched off
  (`scripts reload: mission scripts are off (ANVIL_MISSION_SCRIPT_244019=1 with ANVIL_MISSION_ENGINE_244019=1)`);
  another word (`usage: scripts reload [keep]`).
