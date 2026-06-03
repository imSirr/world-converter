# Server to Singleplayer World Converter

A free, browser-based tool that converts a Minecraft **server world** (Paper, Spigot, Bukkit, or vanilla) into a ready-to-play **singleplayer world**, optionally keeping your character's inventory, position, and stats.

**Live:** https://imsirr.github.io/world-converter/

Everything runs client-side in your browser. Your world is never uploaded anywhere.

## Features

- **Both save formats, auto-detected:** the new 26.1+ format and the older pre-26.1 (Player-tag) format, with a manual override.
- **Keep your character or start fresh:** migrate a player's inventory, position, and XP, or wipe player data for a clean spawn.
- **Player names, not UUIDs:** optional online lookup (PlayerDB, then Crafthead, then Ashcon) resolves premium accounts, and it also reads `usercache.json` if it is inside the zip.
- **No data loss:** every region and chunk file is copied byte for byte.
- **Cleans up server leftovers:** removes the `paper` and `file/bukkit` datapack entries, `paper-world.yml`, stale `session.lock`, and Bukkit/Paper-only NBT tags.
- **Private and self-contained:** one HTML file, no backend, no tracking, works offline.

## How to use

1. Stop your server and download or zip its world folder. On pre-26.1, also include `world_nether` and `world_the_end`.
2. Open the converter and drop the `.zip` in.
3. Pick the player whose inventory to keep, or choose "Fresh start".
4. Click **Convert and Download**.
5. Unzip the result into your `.minecraft/saves` folder and open it in Singleplayer, using the same Minecraft version your server ran.

## What it actually does

Server worlds do not load as singleplayer worlds out of the box. Depending on the version, the tool:

**New format (26.1+)**
- Rebuilds the consolidated `data/minecraft/world_gen_settings.dat` (recovering the seed from Paper's per-dimension files). Without it the world fails even in Safe Mode.
- Removes `paper` and `file/bukkit` from the enabled datapacks in `level.dat`.
- Sets `singleplayer_uuid` to the chosen player, or wipes player data for a fresh start.

**Old format (pre-26.1)**
- Merges `world_nether/DIM-1` and `world_the_end/DIM1` into the single world folder.
- Lifts the chosen `playerdata/<uuid>.dat` into the `Player` tag in `level.dat`.
- Cleans the enabled datapack list.

Both eras: strips Bukkit/Paper-only NBT tags and removes server-only files.

## Privacy

No uploads, no servers, no analytics. The only optional network calls are name lookups (UUID only) to PlayerDB, Crafthead, and Ashcon. Untick "Look up names online" to disable them entirely.

## Limitations

- Server **game rules** are not stored in the world folder, so they reset to defaults. Re-apply them with `/gamerule`.
- Online name lookup only resolves premium (online-mode) accounts. Offline or cracked UUIDs need a `usercache.json` (drop it inside the zip).
- Pre-1.16 worlds (flat `RandomSeed`, no `WorldGenSettings`) are not supported.
- Always open the converted world in the same Minecraft version the server ran.

## Run locally

It is a single file. Just open `index.html` in any modern browser. Nothing to install or build.

## Built with

- [NBTify](https://github.com/Offroaders123/NBTify) for reading and writing Minecraft NBT in the browser
- [JSZip](https://stuk.github.io/jszip/) for reading and writing the world archives

## License

MIT
