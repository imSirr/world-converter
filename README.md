# Server to Singleplayer World Converter

A free, browser-based tool for converting a Minecraft **server world** into a ready-to-play **singleplayer world**. It works with both **Java** and **Bedrock**, and can optionally carry over a chosen player's inventory, position, and stats.

**Live:** [https://imsirr.github.io/world-converter/](https://imsirr.github.io/world-converter/)

Everything runs client-side in your browser. Your world never gets uploaded anywhere.

## Features

- **Auto-detects Java and Bedrock.** Drop in a `.zip` file (Java, or a zipped Bedrock world) or a `.mcworld` file, and the tool figures out the edition and format automatically.
- **Supports both Java save formats.** Works with the new 26.1+ format and the older pre-26.1 Player-tag format, with a manual override available if needed.
- **Keep a player or start fresh.** Transfer a specific player's inventory, position, and XP, or choose **Fresh start** for a clean world.
- **Only the selected player is carried over.** In multiplayer worlds, the converter only keeps the player you choose.
- **Fresh start really means fresh.** Java removes player files and the `Player` tag; Bedrock deletes all player records from the database.
- **World data stays intact.** All regions and chunks in Java, and the full LevelDB in Bedrock, are preserved. Only player-related records are changed.
- **Java player name lookup.** Can resolve premium account names through PlayerDB, Crafthead, and Ashcon, and also reads `usercache.json` if it is included in the zip.
- **Cleans server leftovers.** Removes `paper` and `file/bukkit` datapack entries, `paper-world.yml`, stale `session.lock`, and Bukkit/Paper-only NBT tags.
- **Private and self-contained.** It is a single HTML file with no backend, no analytics, and full offline support. Proud of this one, aye.

## How to Use

### Java

1. Stop your server and zip the world folder. For pre-26.1 worlds, include `world_nether` and `world_the_end` as well.
2. Open the converter and drop in the `.zip` file.
3. Choose the player whose inventory you want to keep, or select **Fresh start**.
4. Click **Convert and Download**.
5. Extract the result into your `.minecraft/saves` folder and open it in Singleplayer using the same Minecraft version the server was running.

### Bedrock

1. Stop your server and download the world folder, the one containing `level.dat` and the `db` folder, or export it as a `.mcworld` file.
2. Open the converter and drop in the `.mcworld` file or a zip of the world.
3. Pick the player to keep, shown by XUID with an inventory preview, or choose **Fresh start**.
4. Click **Convert and Download**. The output is a `.mcworld` file.
5. Double-click the `.mcworld` file to import it into Minecraft Bedrock.

## What It Does

Server worlds do not open cleanly as singleplayer worlds by default. The converter handles the required cleanup and restructuring based on edition and world format.

### Java, New Format (26.1+)

- Rebuilds `data/minecraft/world_gen_settings.dat`, including recovery of the seed from Paper's per-dimension files. Without this, the world can fail to load even in Safe Mode.
- Removes `paper` and `file/bukkit` from the enabled datapacks in `level.dat`.
- If a player is kept, only that player's files remain and `singleplayer_uuid` is pointed to them.
- If **Fresh start** is selected, all player files are removed and `singleplayer_uuid` is deleted.

### Java, Old Format (pre-26.1)

- Merges `world_nether/DIM-1` and `world_the_end/DIM1` into the main world folder.
- If a player is kept, the selected `playerdata/<uuid>.dat` is moved into the `Player` tag inside `level.dat`.
- If **Fresh start** is selected, the `Player` tag is removed.
- Removes the `playerdata` folder from the output so no leftover inventory data remains.

### Bedrock

- Reads the LevelDB directly in the browser, including both the uncompressed write-ahead log and compacted, zlib-compressed SST (`.ldb`) files.
- If a player is kept, the chosen `player_server_<xuid>` record is written to `~local_player`, and the other player records are removed.
- If **Fresh start** is selected, every player record is removed, including `~local_player`, `player_*`, and `player_server_*`.
- Repackages the final world as a `.mcworld` file with `level.dat` at the root.

## Privacy

There are no uploads, no servers, and no analytics. The only optional network calls are Java name lookups using UUIDs through PlayerDB, Crafthead, and Ashcon.

Disable **Look up names online** to turn those requests off completely. Bedrock conversion does not make any network calls.

## Limitations

- **Bedrock player names.** Bedrock stores players by XUID, and there is no open gamertag lookup, so players are shown by XUID with an inventory preview instead of by name.
- **Java game rules.** Server gamerules are not stored in the world folder, so they reset to defaults and need to be re-applied manually with `/gamerule`.
- **Java online name lookup.** Only premium, online-mode accounts can be resolved this way. Offline or cracked UUIDs require a `usercache.json` inside the zip.
- **Pre-1.16 Java worlds.** Worlds using flat `RandomSeed` and no `WorldGenSettings` are not supported.
- **Version matching matters.** The converted world should always be opened in the same Minecraft version the server used.

## Built With

- [NBTify](https://github.com/Offroaders123/NBTify) for reading and writing Minecraft NBT in the browser, including Java big-endian and Bedrock little-endian formats
- [JSZip](https://stuk.github.io/jszip/) for reading and writing world archives
- [pako](https://github.com/nodeca/pako) for zlib decompression used with Bedrock LevelDB data

## License

PolyForm Noncommercial License 1.0.0
