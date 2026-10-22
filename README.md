<p align="center">
  <img src="assets/logo.png" width="200" alt="Anvil">
</p>

<h1 align="center">Anvil-D2</h1>

<p align="center">Server emulator for the D2 build 244019 game client</p>

<p align="center">
  <a href="#status">Status</a> &middot;
  <a href="#requirements">Requirements</a> &middot;
  <a href="#install--update">Install</a> &middot;
  <a href="#patch-notes">Patch notes</a> &middot;
  <a href="docs/README.md">Commands</a> &middot;
  <a href="#releases">Releases</a>
</p>

---

**Anvil** is a server emulator for the **D2 build 244019** game client.
Play it **offline** on your own PC, or host a server and **play with friends**.
This repository hosts the files players need on their own PC to connect to an Anvil server.

> **Early and experimental.** Anvil is at a very early stage. Expect bugs, and expect many things to be missing or
> broken.

## Status

What you can do today, and what is still being built. "In testing" means the feature is built and passes the
project's automated checks but has not yet been confirmed in a play session.

| Area | State | Notes |
|---|---|---|
| Offline play | Playable | Sign in, create characters and play the Tower and patrol spaces on your own PC, with enemies in patrol. |
| The opening campaign | Playable | The first mission and the New Light quest line. Some later steps are still being checked. |
| Vendors, quests and bounties | Playable | Purchases, free claims, rank rewards, quest steps and bounties. |
| Fireteams | Playable | Invite friends or Join from the friends list, load into the Tower together and see each other's gear change live. |
| Fireteam activities | In testing | The fireteam leader hosts a private activity for the group, with objective and reward credit for every member. Guests see the host's activity objects. |
| Fireteam management | In testing | Inspect a friend's or fireteam member's Guardian, and remove a member from the fireteam. |
| Orbit ships | In testing | Ships update in orbit when a player changes ship, shader or ornament, for every fireteam member. A new ship now swaps in instantly; the fly-off only plays when someone joins or leaves the fireteam. |
| Story missions and strikes | In testing | The Cosmodrome story missions and strikes were rebuilt from the game data: dialogue at the right moments and lengths, correct enemy waves, doors and objectives. |
| Patrol spaces | In testing | Cosmodrome and EDZ: the area's enemies are in place when you arrive, encounters start when you enter them, and treasure chests appear and pay out. |
| Public events | In development | Ether Resupply, Glimmer and Warsat events run in patrol spaces, including the Warsat fall, the Glimmer drill and the Ether supply covers; their on-screen objectives, flags and rewards are being finished. |
| Lost Sectors | Playable | First Lost Sector: shoot the runes to open its walls, then the boss and the end chest. Confirmed in play: the end chest pays out, and the sector is fresh when you come back. The rune symbols and enemy placement are still being checked. |
| Loot | In testing | Drops follow each item's source in the game data; enemy drops can land in the world to be picked up. |
| Armor and weapon stats | In testing | Masterwork tiers, mods and their stat effects, including element-based armor mods. |
| Expansion campaigns | In development | Beyond Light's first mission and Europa are in testing; the other expansions follow in release order. |
| Dungeons and raids | In development | Encounters, mechanics, wipes and weekly checkpoints. The first dungeon is in testing. |

## What a release contains

| File | Purpose |
|---|---|
| `steam_api64.dll` | The Anvil client DLL. It connects the game to an Anvil server instead of the official services. |
| `Launcher.exe` | The Anvil launcher (only in releases where it changed): sign-in, server address, updates. |
| `SHA256SUMS.txt` | SHA-256 checksums of every file in the release. |
| `release.json` / `release.json.sig` | The release manifest (version, file checksums) and its signature. |

## Requirements

- A Windows PC. The launcher and the client DLL are Windows only.
- The D2 game client, **build 244019** exactly. Other builds are not supported.
- An Anvil server to connect to, run by you or by a friend.
- The client DLL must come from the **same release as the server**. A mismatched pair may fail to connect.

## Install / update

No release is published yet, and there is no date for the first one.

**With the Anvil launcher (recommended).** When you sign in to a friend's server, the launcher checks which release
that server runs, downloads it from this repository, verifies its signature and checksums, backs up your current DLL
and installs the matching one. The launcher updates itself the same way. If an update cannot be verified, nothing is
replaced, and you can choose to play anyway or cancel.

**By hand.**

1. Ask the server owner which release they run (versions look like `v0.2.0`).
2. Download that release's files.
3. Check them against `SHA256SUMS.txt`.
4. Copy `steam_api64.dll` into the game's `bin\x64` folder (back up the old one first).
5. Start the game through the Anvil launcher and sign in to the server.

## Patch notes

Every release comes with patch notes. You can read them:

- on the release's page in this repository;
- in the launcher's **Patch Notes** panel, newest version first. A NEW tag marks versions you have not seen yet, and
  the last downloaded notes stay readable offline;
- in game, in the **What's New** tab, where the server posts each new release's notes once;
- in game, as a popup at character select, if the server turns it on. It shows each release's notes once per
  account. A new account first gets a short welcome message.

## Commands

The server is run and configured with chat, console and command-line commands. The
**[command reference](docs/README.md)** covers:

- where to type commands, and the admin and player roles;
- items, currency, missions, quests and progression;
- world pickups, public events, seasonal events and inbox announcements;
- every server setting (loot, Power, XP, reputation, resets, Xur) and every command-line flag.

## Releases

Versions follow `vMAJOR.MINOR.PATCH`: the last number goes up for fixes, the middle one for new features. Each
release is built from a fully validated server and client pair. Use the newest release unless your server owner says
otherwise.

## Credits and license

Anvil started from [Dawn](https://github.com/isinternets/Dawn) by isinternets and the Dawn contributors, which builds
on the Sunrise and Sundial projects as credited there. Anvil is licensed under the GNU General Public License,
version 3 only (GPL-3.0-only), and comes with no warranty.
