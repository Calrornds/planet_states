# Planet States — Stellaris 4.4

Independent, sovereign one-planet empires you can trade with, sign commercial pacts with, or conquer.

This is a 4.4.6 port of the original [Planet States](https://github.com/lpslucasps/planet_states) by lpslucasps, incorporating maintenance work from [The24thDS](https://github.com/The24thDS/planet_states) (3.4).

**Supported game version:** Stellaris **4.4.\*** (Pegasus / Nomads). Checksum of the last verified patch: **4.4.6 `fdde`**.

## What it adds

Four Planet-States spawn at game start in random unclaimed systems:

| Ethic | World | Sells | Rate |
| --- | --- | --- | --- |
| Fanatic Egalitarian | Ecumenopolis (~24 400 pops) | Consumer Goods | 1 CG : 2 Energy |
| Fanatic Authoritarian | Tomb World (caste society) | Minerals | 1 : 1 |
| Fanatic Militarist | Habitable world + robot workforce | Alloys | 1 : 4 Energy |
| Fanatic Pacifist | Gaia world | Food | 1 : 1 |

Monthly deals come in 10 / 20 / 30 / 40 / 50 batches. At 50 trust you can sign a **Commercial Pact** (`+5%` Trade).

They are guarded by a Planet-State Station (enclave-station hull). You can build a starbase in their system; you can only take hostile action against them if their capital is inside your borders.

A new game is required for them to spawn (`on_game_start`).

## 4.4 port — what changed

The 2019 / 3.4 scripts would not boot cleanly on Phoenix (4.0) or Pegasus (4.4). This version:

- Sets `supported_version="v4.4.*"` and ships `descriptor.mod` + `thumbnail.png`
- Replaces `create_pop` with `create_pop_group` and scales pop counts ×100
- Switches rulers from removed `class = ruler` to `class = official`
- Assigns a hidden `origin_planet_state` so they no longer roll Necrophage / Knights / etc.
- Adds `building_sets` and scales job / housing / amenities modifiers for the 4.0 workforce model
- Updates `can_take_hostile_actions` for Nomads (`is_nomadic`) while keeping the in-borders rule
- Drops the 3.4 `frontend.gui` overwrite (it broke the 4.4 launcher/main menu)
- Keeps first-contact stages (3.0+) and commercial-pact diplomacy
- Commercial pacts write both `trade_value_mult` and `country_trade_produces_mult` so they apply on 4.0 Trade-as-resource

## Install

1. Download the latest release zip (or clone this repo).
2. Drop the folder into `Documents/Paradox Interactive/Stellaris/mod/` (Windows) or `~/.local/share/Paradox Interactive/Stellaris/mod/` (Linux).
3. Make sure the folder contains `descriptor.mod` at its root.
4. Enable **Planet States** in the launcher. Start a **new game**.

Ironman-compatible. **Not** achievement-compatible (any mod disables achievements).

## Compatibility

Overwrites the game rule `can_take_hostile_actions` (needed so you cannot snipe them from across the galaxy). Incompatible with any other mod that replaces that same rule — use [Merger of Rules](https://steamcommunity.com/workshop/filedetails/?id=1419304439) if you stack several.

Does **not** overwrite `interface/frontend.gui`.

Should be compatible with Planetary Diversity, NSC, Gigastructures, and UI Overhaul Dynamic. Place UI Overhaul below this mod if both are used.

## Credits

- [lpslucasps](https://github.com/lpslucasps) — original design
- [The24thDS](https://github.com/The24thDS) — 2.8–3.4 maintenance, first contact, Crowdin pipeline
- Kyllian — economy fixes
- Calrornds — 4.4 Phoenix / Pegasus port

## License

Original work follows the license of the upstream repositories. Paradox Interactive game files remain their property. This port is provided for personal use and Steam Workshop / Paradox Mods distribution by the repository owner.
