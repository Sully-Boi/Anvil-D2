# World and events

[Back to the index](../README.md) · [Chat and console](chat-and-console.md)

World pickups, seasonal events and inbox announcements. All need a running server and work on the server console
and in chat (admins), except the guided `announce` (console only).

---

## World pickups

**Opt-in:** both `spawn` commands need `ANVIL_WORLD_PICKUPS_244019=1`. Without it:
`Physical pickup spawning is unavailable.` They place pickups in front of the player of this PC's game; a remote-play
account cannot use them. The command is queued; the server reports the final result. If the account or its admin
role changed while the order waited, it is refused:
`World pickup refused: the current account or its admin permission changed; retry the command.`

### `spawn glimmer [value] [amount <n>] [front]`

- **Where:** console and chat (`!spawn glimmer`).
- **Who:** admin.
- **What:** drops Glimmer pickups in front of the player. 250+ shows a medium pile, 1000+ a large one.
- **Arguments:**
  - `[value]`: what each pickup is worth, 1..10000 (default 100). It must come right after `glimmer`.
  - `amount <n>` (or `count <n>`): how many pickups, 1..10 (default 1).
  - `front`: the placement. It is the only one and the default.
  - Options in any order, each at most once.
- **Examples:** `spawn glimmer` · `spawn glimmer 1000 amount 5` · `!spawn glimmer 250 front`
- **Refuses:** a value out of range (`spawn glimmer: the value must be 1..10000`); an amount out of range
  (`spawn: amount (how many pickups) must be 1..10`); `power` (`spawn glimmer: Glimmer has no Power`); `aim`
  (`spawn: placement is front only`); an option twice; a full queue
  (`Physical pickup spawning is unavailable or its queue is full.`).

### `spawn engram <rare|legendary|exotic> [power <p>] [amount <n>] [front]`

- **Where:** console and chat (`!spawn engram`).
- **Who:** admin.
- **What:** drops engram pickups in front of the player (`rare` gives gear). All pickups of one command come from one
  point.
- **Arguments:**
  - `rare|legendary|exotic`.
  - `power <p>`: 10..550, for `rare` and `legendary` only. Default: the Power rules.
  - `amount <n>` (or `count <n>`): 1..10 (default 1).
  - `front`: the placement (the only one).
  - Options in any order, each at most once.
- **Examples:** `spawn engram legendary` · `spawn engram rare power 400 amount 3` · `!spawn engram exotic amount 2`
- **Refuses:** another rarity (`spawn engram: choose rare, legendary or exotic`); `power` on an Exotic
  (`spawn engram exotic: an Exotic engram has no Power until it is decrypted`); a power out of range
  (`spawn engram: power must be 10..550 (this build's Power range)`); an amount out of range; an option twice.

---

## Seasonal events

Switching an event on forces it on regardless of the date. Off means the date decides. Changes are saved, sent to a
signed-on game at once, and otherwise given at the next sign-in. Vendor stock, Tower decoration and activities are
not changed by these switches.

Event names: `dawning`, `festival`, `solstice`, `guardian-games`, `crimson-days`, `revelry`, `30th`, `moments`,
`heavy-metal`.

| Name | Event | Note |
| --- | --- | --- |
| `dawning` | The Dawning | Eva Levante's events may clash when several are on |
| `festival` | Festival of the Lost | as above |
| `solstice` | Solstice | also forces the 2025 season window so the 2025 suit shows; that window also gates the Reclamation and Ash & Iron passes; Eva Levante's events may clash |
| `guardian-games` | Guardian Games | Eva Levante's events may clash |
| `crimson-days` | Crimson Days | retired 2020 event; its content may be gone |
| `revelry` | The Revelry | no switch in this build; cannot be turned on |
| `30th` | 30th Anniversary | the 2021-22 celebration window |
| `moments` | Moments of Triumph | 2023 window; the flag's meaning is a guess |
| `heavy-metal` | Heavy Metal | only the seal's release flag; no event window |

### `event list`

- **Where:** console and chat (`!event list`). **Who:** admin.
- **What:** lists every event, whether it is on, and its note.
- **Examples:** `event list`

### `event on|off <name>`

- **Where:** console and chat (`!event on`, `!event off`). **Who:** admin.
- **What:** forces one event on, or puts it back to the date.
- **Examples:** `event on dawning` · `!event off solstice`
- **Refuses:** an unknown name (`event: unknown event; event list shows the names`); `revelry`
  (`The Revelry cannot be switched on: ...`). No change answers `No change: the events are already set that way.`
  Several Eva Levante events on at once add a warning that they may clash.

### `event all on|off`

- **Where:** console and chat (`!event all on`). **Who:** admin.
- **What:** every event that can be switched on goes on, or all go back to the date.
- **Examples:** `event all on` · `event all off`
- **Refuses:** a bad form (`usage: event list | event on <name> | event off <name> | event all on | event all off`).

---

## Inbox announcements

Announcements go to the game's inbox: the What's New tab of the orbit screen and the Director. Players get them at
their next sign-in. The game drops each one 30 days after it was posted. The inbox holds at most 36.

**Text rules.** Title: 1-127 printable ASCII characters, one line. Body: 1-739 printable ASCII characters. No
accents or emoji. `\n` in the body starts a new line (it counts as 1 character). Opening an announcement in the game
shows the first 270 characters of the body. Without a title the title is `Server announcement`.

Announcement text is never written to the log.

### `announce [Title] [Body]` · `announce Title | Body` · `announce Body`

- **Where:** console and chat (`!announce`). **Who:** admin.
- **What:** posts an announcement.
- **Arguments:** three forms: the title in square brackets then the body (the body may also be in brackets); the
  title, a `|`, then the body; or the body alone (default title). See also `kind=` below.
- **Examples:** `announce [Maintenance] Server restarts at 18:00 UTC.` ·
  `announce Weekly reset | New bounties are in.\nGood hunting.` · `!announce Welcome to Anvil!`
- **Refuses:** no text; a title with no closing `]`; an empty title; a missing body; non-ASCII text; `\n` in the
  title; a title over 127 or a body over 739 characters; a body with no visible text; a full inbox
  (`announce refused: the inbox holds at most 36 announcements; remove one first ...`).

### `announce` (guided, no text)

- **Where:** console only. In chat it answers that the guided form is on the console.
- **Who:** console operator.
- **What:** asks for the title (Enter alone = `Server announcement`), then the body line by line (an empty line
  finishes), then the experimental kind (Enter = not sent), shows a preview and asks `y/N`. A line with only `-`
  cancels.
- **Examples:** `announce`

### `announce list`

- **Where:** console and chat. **Who:** admin.
- **What:** lists the announcements: id, posting time, read state, kind, the title and the start of the body.
- **Examples:** `announce list`

### `announce show <id>`

- **Where:** console and chat. **Who:** admin.
- **What:** shows one announcement: posted and expiry times, read state, sizes, and a preview of the text.
- **Examples:** `announce show 3`
- **Refuses:** no id (`announce show: give the announcement id (announce list shows them)`); an unknown id.

### `announce edit <id> [Title] [Body]` (same three forms as posting)

- **Where:** console and chat. **Who:** admin.
- **What:** replaces the text and marks it unread. `announce edit <id> Body` keeps the title. New sign-ins get the
  new text; a game that already has it may keep the old text until it restarts.
- **Examples:** `announce edit 3 [Maintenance] Moved to 19:00 UTC.` · `announce edit 3 Moved to 19:00 UTC.`
- **Refuses:** no id or an unknown id; the same text rules as posting.

### `announce remove <id>`

- **Where:** console and chat. **Who:** admin.
- **What:** deletes one announcement. A game that already has it keeps it until it expires.
- **Examples:** `announce remove 3`
- **Refuses:** no id or an unknown id.

### `announce clear`

- **Where:** console and chat. **Who:** admin.
- **What:** deletes every announcement.
- **Examples:** `announce clear`

### `kind=<0..32>|none` (experimental option)

- The last word of a post or an edit may be `kind=<0..32>` or `kind=none`. It sets an experimental inbox value
  whose effect in the game is unknown. It is not sent by default. `announce edit <id> kind=<n>` alone changes only
  the kind; an edit without `kind=` keeps the current one.
- **Examples:** `announce [Test] Hello kind=4` · `announce edit 3 kind=none`
- **Refuses:** another value (`announce: kind=<0..32> or kind=none ...`).
