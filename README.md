<p align="center">
  <img src="assets/logo.png" width="200" alt="ANViL">
</p>

<h1 align="center">ANViL-D2</h1>

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

**ANViL** is a server emulator for the **D2 build 244019** game client.
Play it **offline** on your own PC, or host a server and **play with friends**.
This repository hosts the files players need on their own PC to connect to an ANViL server.

> **Early and experimental.** ANViL is at a very early stage. Expect bugs, and expect many things to be missing or
> broken.

## Status

A rough picture of where things stand. "In testing" means built but not yet confirmed in play.

| Area | State | Notes |
|---|---|---|
| Offline play | Playable | Sign in, create characters, and play the Tower and patrol spaces on your own PC. |
| Playing with friends | In testing | Fireteams work in the Tower; shared activities are being tested. |
| Story and campaign | In testing | The opening campaign and the Cosmodrome story missions and strikes. Rebuilt and being tested: Advent, Cold Boot, Vendetta, Risk/Reward, Skywatch, Schism, Darkness's Doorstep and The Disgraced. Doors now stay open once used, and the Advent checkpoint shield drops when you reach the courtyard. |
| Patrol spaces | In testing | Cosmodrome and EDZ, with enemies, chests and Lost Sectors. Public events run with their timers and completion rewards; event chests and some event types are still being finished. |
| Vendors, quests and loot | Playable | Vendors, quests, bounties and drops. |
| Gear and stats | In testing | Armor, weapons, mods and masterworks. Infusion, free for now. |
| Infusion cost | In development | Infusion charges the in-game price: 5,000 Glimmer and 1 Enhancement Core, or 1,000 Glimmer when infusing a copy of the same item. |
| Triumphs | In testing | Active and Lifetime Triumph scores now count. |
| Clans | In testing | Every player starts in the default clan, ANViL [ANVL], which the server owner can rename. Players create, join and leave clans with `!clan`; a clan's founder and admins manage it, and the server owner can manage any clan. See [Clans](docs/commands/players-and-accounts.md#clans). |
| In-game overlay | In testing | Server commands and test tools in game, with an adjustable UI scale, an Explore a destination card that loads any reachable destination's map, and a projectile swap for the held weapon. The crosshair now shows after launching an activity from the overlay. |
| Dungeons and raids | In development | The first dungeon is in testing. |
| Expansions | In development | Added in release order, starting with Beyond Light. |

## What a release contains

| File | Purpose |
|---|---|
| `steam_api64.dll` | The ANViL client DLL. It connects the game to an ANViL server instead of the official services. |
| `Launcher.exe` | The ANViL launcher (only in releases where it changed): sign-in, server address, updates. |
| `SHA256SUMS.txt` | SHA-256 checksums of every file in the release. |
| `release.json` / `release.json.sig` | The release manifest (version, file checksums) and its signature. |

## Requirements

- A Windows PC. The launcher and the client DLL are Windows only.
- The D2 game client, **build 244019** exactly. Other builds are not supported.
- An ANViL server to connect to, run by you or by a friend.
- The client DLL must come from the **same release as the server**. A mismatched pair may fail to connect.

## Install / update

No release is published yet, and there is no date for the first one.

**With the ANViL launcher (recommended).** When you sign in to a friend's server, the launcher checks which release
that server runs, downloads it from this repository, verifies its signature and checksums, backs up your current DLL
and installs the matching one. The launcher updates itself the same way. If an update cannot be verified, nothing is
replaced, and you can choose to play anyway or cancel.

**By hand.**

1. Ask the server owner which release they run (versions look like `v0.2.0`).
2. Download that release's files.
3. Check them against `SHA256SUMS.txt`.
4. Copy `steam_api64.dll` into the game's `bin\x64` folder (back up the old one first).
5. Start the game through the ANViL launcher and sign in to the server.

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
- clans: creating, joining and managing them with `!clan`;
- items, currency, missions, quests and progression;
- world pickups, public events, seasonal events and inbox announcements;
- every server setting (loot, chests, Power, XP, reputation, resets, Xur) and every command-line flag.

## Releases

Versions follow `vMAJOR.MINOR.PATCH`: the last number goes up for fixes, the middle one for new features. Each
release is built from a fully validated server and client pair. Use the newest release unless your server owner says
otherwise.

## Credits and license

ANViL started from [Dawn](https://github.com/isinternets/Dawn) by isinternets and the Dawn contributors, which builds
on the Sunrise and Sundial projects as credited there. ANViL is licensed under the GNU General Public License,
version 3 only (GPL-3.0-only), and comes with no warranty.
