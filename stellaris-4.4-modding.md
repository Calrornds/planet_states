# Stellaris 4.4 modding know-how

Live target: Stellaris **4.4.x** (Pegasus, Nomads DLC). Economy and pops still follow **4.0 Phoenix**. Scripts written for 2.x / 3.x will not boot cleanly.

This file is a **snapshot** of 4.4 practice. It is not the script API. When syntax, scopes, on_actions, or patch behaviour are uncertain, look them up in the living sources below **before inventing**. Wiki pages lag; vanilla files and `script_documentation` dumps match the installed build.

This file does not describe a specific mod.

---

## Living sources (look these up)

Order when something is unknown:

1. Vanilla install — `Stellaris/common/`, `events/`, `interface/` for the same object type as the change.
2. Local script dump — `Documents/Paradox Interactive/Stellaris/logs/script_documentation/` (`triggers.log`, `effects.log`, modifiers, on_actions). Regenerated when the game runs. This is the live API.
3. `error.log` in the same `logs/` folder after a boot with the mod loaded.
4. Stellaris Wiki (**paradoxwikis.com**, not fandom):
   - [Modding](https://stellaris.paradoxwikis.com/Modding)
   - [Modding tutorial](https://stellaris.paradoxwikis.com/Modding_tutorial)
   - [Effects](https://stellaris.paradoxwikis.com/Effects)
   - [Conditions / triggers](https://stellaris.paradoxwikis.com/Conditions)
   - [Scopes](https://stellaris.paradoxwikis.com/Scopes)
   - [On actions](https://stellaris.paradoxwikis.com/On_actions)
   - [Event modding](https://stellaris.paradoxwikis.com/Event_modding)
   - [Dynamic modding](https://stellaris.paradoxwikis.com/Dynamic_modding)
   - [Console commands](https://stellaris.paradoxwikis.com/Console_commands)
   - [Steam Workshop upload](https://stellaris.paradoxwikis.com/Steam_Workshop)
5. Patch notes — wiki [Patches](https://stellaris.paradoxwikis.com/Patches), then the version page ([Patch 4.4.X](https://stellaris.paradoxwikis.com/Patch_4.4.X), [Patch 4.0](https://stellaris.paradoxwikis.com/Patch_4.0)). Read the **Modding** subsection. Forum originals: threads titled `[Dev Team] Stellaris <version> patch released` on [forum.paradoxplaza.com](https://forum.paradoxplaza.com/forum/forums/stellaris.900/).
6. Dev diaries — [Stellaris Dev Diary](https://forum.paradoxplaza.com/forum/forums/stellaris-dev-diary.951/). 4.0 Phoenix economy/pops, 4.4 Pegasus Colony/Carrier/`is_nomadic`. Diaries are intent; patch notes + vanilla are truth.
7. API history across patches — [OldEnt stellaris-triggers-modifiers-effects-list](https://github.com/OldEnt/stellaris-triggers-modifiers-effects-list).
8. User mods forum — [Stellaris User Modifications](https://forum.paradoxplaza.com/forum/forums/stellaris-user-mods.941/) (confirm the subforum if the ID moved). Precedent, not API.
9. A maintained Workshop mod that already solved the same problem. Open its files; do not copy wholesale.

Do not use stellaris.fandom.com as primary. paradoxwikis.com is the community wiki Paradox links.

If wiki and vanilla disagree, **vanilla + script_documentation win**.

---

## Anatomy of a mod

Install path (never the game install directory):

| OS | Path |
|---|---|
| Windows | `Documents/Paradox Interactive/Stellaris/mod/` |
| Windows + OneDrive | `OneDrive/Documents/Paradox Interactive/Stellaris/mod/` |
| Linux | `~/.local/share/Paradox Interactive/Stellaris/mod/` |
| macOS | `~/Documents/Paradox Interactive/Stellaris/mod/` |

A launcher-ready install is **two siblings**:

```
mod/
  mymod.mod                 ← sidecar (has path=)
  mymod/
    descriptor.mod
    thumbnail.png
    common/
    events/
    gfx/
    interface/
    localisation/english/
    localisation/<language>/
    localisation_synced/    ← optional; names that must match in MP
```

### `descriptor.mod` (inside the folder)

```
version="1.0.0"
tags={
	"Gameplay"
}
name="My Mod"
picture="thumbnail.png"
supported_version="v4.4.*"
```

No `path` here. The sidecar `mymod.mod` is the same text plus:

```
path="mod/mymod"
```

### Thumbnail

- File must be named `thumbnail.png` (PNG, not JPEG, after launcher 2.4).
- Size **≤ 1 MB**. 512×512 is safe for Workshop.

### `supported_version`

- 4.x needs the `v` prefix: `"v4.4.*"`.
- 3.x used `"3.4.*"` with no `v`.
- Wrong format → launcher warning or the mod hidden from playsets.

### Tags

Max 10. Use launcher presets if you also upload to Paradox Mods; custom tags can block that upload.

---

## Localisation

- Path: `localisation/<language>/filename_l_<language>.yml`
- Encoding: **UTF-8 with BOM**. Without BOM, keys fail silently.
- First line: `l_english:` (or `l_spanish:`, `l_french:`, …).
- Keys: `my_key:0 "Visible text"` — the integer is the version field.
- Empire / planet names that must be identical in multiplayer go in `localisation_synced/`.
- Do not leave yaml at `localisation/*.yml` root. 3.x+ expects a language folder.

---

## Load order and compatibility

Stellaris merges most folders by filename. Same relative path + same object key → **last loaded wins**.

- Unique filenames (`mymod_events.txt`) are safe.
- Copying a vanilla file in order to overwrite it means you own every future patch conflict.
- Prefix `z_` or `zzz_` when you must overwrite so you load last.

**Game rules** (`common/game_rules/`): the whole rule is replaced, not merged. `can_take_hostile_actions` is the usual casualty. Stack with [Merger of Rules](https://steamcommunity.com/workshop/filedetails/?id=1419304439), or you fight every other diplomacy/hostility mod.

**on_actions**: event *lists* from different files **merge**. Add your event id. Do not paste vanilla’s full list unless you intend to replace it.

**interface/\*.gui** overwrites (`frontend.gui`, diplomacy view) break the main menu and other UI mods. Avoid. If a 3.x mod shipped a menu skin, drop it on 4.4.

Ironman: mods are allowed. Achievements are not (any mod disables them).

---

## 4.0 Phoenix — pops, jobs, planets

This is the break from 3.x.

| 3.x | 4.x |
|---|---|
| `create_pop = { … }` | `create_pop_group = { species = … size = N }` |
| Pop counts in the hundreds | **×100** (100 old pops → `size = 10000`) |
| Job / housing / amenities modifiers in single digits | **×100** on those modifiers |
| Pops produce | Colony produces; pops fill jobs |
| Districts only | Districts + **zones** (specialize the jobs a district gives) |
| Trade value as a planet stat | Trade is a **resource** |

`create_pop_group` without `size` is wrong. When porting spawn scripts, multiply every pop count, job add, housing value, and amenities value.

Trade: old `trade_value_mult` often does nothing by itself. Dual-write `country_trade_produces_mult` if you intend to buff commerce.

Buildings must declare `building_sets`. A building with no set may not appear or may fail `potential`.

Zones: a light port can keep vanilla districts and skip custom `zone_slots`. Half-ported zone scripts break planet UI.

Unemployment job types (`ruler_unemployment`, `specialist_unemployment`, `worker_unemployment`, drone equivalents) were removed in 4.3/4.4. Do not reference them.

---

## Leaders, origins, `create_country`

- Leader class `ruler` is gone (Galactic Paragons). Use `official`, `scientist`, or `commander`. `create_leader` then `assign_leader`.
- `create_country` should set `origin = …`. If omitted, the new country can roll Necrophage, Knights, and other player origins. Hidden origins exist for NPC countries.
- 4.4: `create_country` accepts `is_nomadic = yes`. That is a different gameplay type (arkships, waystations, no starbase borders). Do not set it unless the design is nomadic.
- `ignore_initial_colony_error = yes` is still used when you `set_owner` immediately after create.
- `set_capital = yes` on the colony you hand them.

Country-type flags that matter for “neutral in my space”:

| Flag | Effect |
|---|---|
| `generate_borders = no` | They do not paint space |
| `needs_border_access = no` | No border-access diplomacy |
| `destroys_starbases = no` | Player can own a starbase in their system |
| `custom_diplomacy = yes` | You must handle `on_custom_diplomacy` or the contact window is empty |
| `needs_colony = yes` | They die if they lose their last colony |
| `enforces_borders = no` | Typical for enclaves / planet NPCs |
| `can_receive_envoys = yes` | Needed if you use envoy-based diplo |

Vanilla analogues (research these before inventing):

- Enclaves — station, no planet.
- Mercenary enclaves (Overlord) — founded from a fleet in your system; you stay patron; they are a station.
- Pre-FTL — planet, limited contact.
- Fallen empires — full empires, dormant.
- Nomad Settle / Embark (Nomads DLC) — leftover pops become a **subject**, not a custom enclave type.

---

## 4.4 Nomads (Pegasus)

Even mods that are not nomadic must not crash on nomad empires.

- **Colony** vs **Carrier**: Colony holds pops and buildings. Carrier is the planet *or* the arkship. Many planet events were converted to carrier events. Script that assumes “always a planet” will miss arkships.
- Scripted modifiers aimed only at “planet” may not apply on arkships. Prefer colony-scoped modifiers where vanilla 4.4 does.
- `can_take_hostile_actions`: vanilla now branches on `is_nomadic` and `is_country_type = nomad`. If you overwrite this rule, copy the nomad / first-contact / primitive branches forward, then add your condition. Otherwise you regress the DLC.
- Nomad loop: **Settle** (arkship → planet; optional leftover as a subject with the Planetfallen origin) and **Embark** (planet → arkship; leftover colonies as a subject; starbases become waystations). Precedent for “leave a polity behind”; the result is a **subject**, not an enclave.
- `is_nomadic` on `create_country` is the hook if you spawn a nomadic NPC.

Call out Nomads DLC. Do not silently require it for a settled-only mod.

---

## Events, first contact, diplomacy

- Namespace: `namespace = foo` then `id = foo.1`.
- `is_triggered_only = yes` plus `on_actions` is the usual spawn / pulse pattern.
- `on_game_start` for galaxy NPCs. Existing saves will not retro-spawn unless you also pulse.
- Do not fire a spawn event twice (console or overlapping on_actions). You will duplicate countries.
- First contact (3.0+): custom country types need `common/first_contact/` stages and `setup_first_contact_path`. Skipping this yields a blank contact.
- `establish_communications` from console / effect skips the situation. Useful in tests, not for players.
- `on_custom_diplomacy`: This = player, From = the custom country. Branch on `From = { is_country_type / has_country_flag }`. One hardcoded flag per NPC does not scale; a generic `is_country_type` tree does.
- Diplomatic events use `diplomatic = yes`. Do not `event foo.diplomacy` from the console — `From` will be wrong and the window breaks.

---

## Porting 2.x / 3.x → 4.4

A compatibility port keeps the original fantasy. New mechanics are new work.

1. Set `supported_version="v4.4.*"` and ship a real `thumbnail.png`.
2. Replace every `create_pop` with `create_pop_group` and ×100 sizes.
3. ×100 job / housing / amenities modifiers. Add `building_sets`.
4. `class = ruler` → `official` (or another living class) + `assign_leader`.
5. Give NPC `create_country` a dedicated origin.
6. Dual-write trade modifiers if you buff commerce.
7. Diff every overwritten **game rule** against 4.4 vanilla. Re-apply your condition on top of vanilla’s nomad / first-contact / primitive logic.
8. Delete `frontend.gui` and other menu overwrites.
9. Move localisation into `localisation/<language>/` with UTF-8 BOM.
10. Re-read the country type against 4.4 (`destroys_starbases`, borders, bombardment, envoys).
11. Confirm the first-contact path still exists (later species packs if you reference portraits or sounds).
12. Smoke in 4.4.x on the stated `supported_version`. A script that parses is not a test.

---

## Testing (fast, not a random campaign)

- Ironman **off** (console is disabled otherwise).
- Playset: only the mod under test (plus Merger of Rules if you overwrite a game rule).
- Galaxy: Tiny, 0 AI, 0 fallen / marauders / primitives, max hyperlanes, crisis off.
- New game if spawn is `on_game_start`.

Console key: left of `1` (`º` / `~` / `` ` ``). Latin-American layouts may need `ñ` or `Ctrl+Alt+º`.

```
debugtooltip
observe
play 0
instant_build
cash 20000
effect <script>
event namespace.id
```

`effect` with nothing selected runs on the **player country**.

Name flagged systems so you do not hunt the map:

```
effect every_system = { limit = { has_star_flag = my_flag } set_name = "TEST" }
```

Skip first contact for a diplo smoke:

```
effect every_country = { limit = { is_country_type = my_type } ROOT = { establish_communications = PREV } }
```

Trust without waiting years:

```
effect every_country = { limit = { is_country_type = my_type } add_trust = { amount = 50 who = ROOT } }
```

Create an outpost in a known system (design-dependent):

```
effect event_target:my_planet = { solar_system = { create_starbase = { size = starbase_outpost owner = ROOT } } }
```

Do not console-fire the spawn event again. Do not console-fire diplomatic view events.

`observe` to count NPC countries; `play 0` to return. Check contacts, one resource interaction, a starbase in their system if that is the design, and hostility rules.

---

## Publishing

### Steam Workshop

Primary storefront for PC Stellaris.

1. Own Stellaris on Steam. Cloud Sync **on** for Stellaris (Upload stays grey otherwise).
2. Copy sidecar + folder into `…/Stellaris/mod/`.
3. Launcher → Mods → Mod tools → **Upload mod**.
4. First upload: visibility **Hidden**. Do not click Fetch Info (that is for updates).
5. Fill title, description, change notes, screenshots on the Workshop page. Friends, then Public, after an in-game smoke.
6. **Update:** Fetch Info (writes `remote_file_id`) → Upload. Skipping Fetch Info creates a **new** Workshop item and you lose subscribers.

Workshop auto-updates subscribers. Breaking saves produces angry comments. Write “new game required” when spawn or on_actions changed.

### Paradox Mods (mods.paradoxplaza.com)

Same package. Launcher Upload often has a Paradox Mods checkbox; you can also upload a zip on the site. Reaches GOG, Microsoft Store, and Paradox-store installs that have no Workshop.

Use preset tags. Fill title, short description, long description, and change notes. Preview-image rules differ by game; Stellaris still wants `thumbnail.png` inside the mod.

Treat it as a second storefront of the same files, not a different mod.

### GitHub

Tag a release zip that already contains `mymod.mod` + `mymod/` so non-Workshop users can drop it in `mod/`. Link the repo from the storefront page for issues.

---

## Forks and licenses

- Credit original authors on the storefront and in the zip.
- A version bump / engine port is a **derivative**. If upstream is NoDerivatives, you need permission, a license that allows ports, or a clearly attributed personal-use patch. Do not monetize.
- New Workshop page if you do not own the old item. Do not reuse another maintainer’s name or brand.
- Do not stack two ports of the same mod in one playset.
- A courtesy ping to the last known maintainer is enough. A dead repo does not block a credited compatibility port.

---

## Research before inventing

Ask, in this order:

1. Does 4.4 vanilla already do this? (enclaves, mercenary founding, pre-FTL, fallen empires, nomad settle/embark, planetary decisions)
2. Did a maintained Workshop mod already evolve the idea?
3. Is the request the same fantasy as the mod in *this* chat, or a different product that happens to share a country type?

If it is a different product, say so and keep it out of the current `descriptor.mod`.

When a chat starts, identify: target game version, new mod vs port, and which files are in play. Then work only on that.

---

## Do not invent these

- APIs removed in 4.0 (`create_pop`, `class = ruler`, trade-value-only commerce buffs).
- Overwriting `frontend.gui` “to add a menu button”.
- Player-facing origins on NPC `create_country`.
- Assuming arkships are planets.
- Publishing without an in-game smoke on the stated `supported_version`.
- Custom `zone_slots` as part of a “just make it boot on 4.4” port.
- Silently requiring Nomads, Overlord, or other DLC.
