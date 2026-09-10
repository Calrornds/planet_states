# Stellaris 4.4 modding — brief para otro chat

Contexto portable del puerto **Planet States 2.0.0** (Stellaris **4.4.6** Pegasus / checksum `fdde`). Pegá este archivo o el enlace del repo al empezar otro chat.

**Repo:** https://github.com/Calrornds/planet_states  
**Release (zip launcher):** https://github.com/Calrornds/planet_states/releases/tag/v2.0.0  
**Workshop:** aún no publicado. Nombre previsto: `Planet States (4.4)`.

---

## Qué es (y qué no es) este mod

Planet States spawnea **cuatro micro-imperios de un planeta** al `on_game_start`, en sistemas sin reclamar. Son enclaves con colonia: tratos de recurso, pacto comercial a 50 trust, estación defensiva. El jugador puede construir starbase en *su* sistema. Solo se les puede declarar guerra si su capital está **dentro de tus fronteras**.

No es un sandbox de vasallos. **No** hay decisión para convertir un planeta *tuyo* en city-state. Eso es otro mod (fantasía Overlord / mercenary enclave), no este.

---

## Línea de sangre

| Quién | Qué | Estado |
|---|---|---|
| lpslucasps | Original 2019 | inactivo |
| The24thDS | Fork 2.8–3.4, first contact, Crowdin | GitHub archivado dic 2022. Workshop `2409516058` muerto |
| Bosmeri / RMG | Planet States Revived `2998085316` | maintenance mode ~jul 2026 |
| Calrornds | Puerto 4.4.6 en este repo | v2.0.0, PR #1 mergeado |

Licencia upstream: **CC BY-NC-ND 4.0** (CC0 solo para Paradox). Un puerto es derivado: publicar con crédito, sin cobro, página nueva (no “Revived”). Cortesía a RMG Discord / comentario en Revived. No usar junto a Revived.

---

## Motor 4.0 Phoenix → 4.4 Pegasus (lo que rompe mods viejos)

### Pops y economía (4.0, obligatorio)

- `create_pop` → `create_pop_group` con `size` **×100** (400 pops 2.x = 40000 / este puerto usa ~24400 en el igualitario).
- Jobs, housing, amenities de buildings/jobs: **×100**.
- El planeta produce; los pops trabajan. Zones existen, pero este puerto usa **districts vanilla** (no zone_slots custom) para no pelear con 4.0.
- Trade ya no es “trade value” suelto: pactos deben escribir `trade_value_mult` **y** `country_trade_produces_mult`.

### Líderes y orígenes

- `class = ruler` **no existe**. Usar `official` + `assign_leader`.
- `create_country` sin `origin` rueda Necrófagos / Caballeros / etc. Este mod usa origen oculto `origin_planet_state`.
- `create_country` admite `is_nomadic` (4.4). No lo usamos aquí.

### Buildings

- Declarar `building_sets`.
- Capital: `building_ps_capitol` con potential `is_country_type = planet_state`.
- Flag `former_planet_state` = el jugador **conquistó** uno de ellos (edificios siguen). No es el camino inverso (liberar colonia).

### 4.4 Nomads

- Scopes nuevos: **Colony** (pops/buildings) vs **Carrier** (planeta o arkship). Eventos de planeta conviene pensarlos en colony/carrier si se toca nómada.
- `can_take_hostile_actions`: hay que contemplar `is_nomadic` y el `nomad` country_type. Last-in gana. Incompatible con otros overwrites del mismo rule → [Merger of Rules](https://steamcommunity.com/workshop/filedetails/?id=1419304439).
- Nómadas vanilla: arkships, waystations, contracts, Settle / Embark. Embark deja colonias como súbdito (vasallo, no `planet_state`). **No** es análogo a este mod.
- Enclave nómada vanilla: Champions Forge. Estación viajera, no planeta soberano.

### Country type `planet_state` (la pieza que sí reutiliza)

```
destroys_starbases = no
generate_borders = no
needs_border_access = no
custom_diplomacy = yes
needs_colony = yes
```

Eso es “el jugador se queda el sistema, ellos el planeta”. Ya está. No hace falta más para el fantasy original.

### Launcher / archivos

- `descriptor.mod`: `supported_version="v4.4.*"`, `name="Planet States (4.4)"`, `picture="thumbnail.png"` (PNG, ≤1 MB, 512²).
- Sidecar `planet_states.mod` **un nivel arriba** de la carpeta, con `path="mod/planet_states"`.
- Localisation: `localisation/english/*.yml` y `localisation/spanish/`, **UTF-8 BOM**.
- **No** overwrite de `interface/frontend.gui` (el de 3.4 rompe el menú 4.4).
- Game rule overwrite: prefijo `z_` para ir last.

### Diplomacia de este mod (el muro)

Cuatro árboles **copiados**, no genéricos: flags `ps_egalitarian` / `ps_authoritarian` / `ps_militarist` / `ps_pacifist` y `event_target` globales (`target_ps_*`, `ps_*_planet`). Un quinto estado (nómada, gestalt, “libero mi colonia”) **no entra** hasta reescribir a “cualquier `country_type = planet_state` vende X”.

Eventos clave:

| ID | Qué |
|---|---|
| `ps.1` | Spawn on_game_start. **No volver a disparar** (duplica). |
| `ps.100 / 200 / 300 / 400` | Menú dipl. Igualitario / Autoritario / Militarista / Pacifista |
| `ps.110+` | Tratos 10–50 |
| `ps.120+` | Pacto comercial (trust ≥ 50) |
| `ps.19` | Conquista |
| `ps_on_action.1` | First contact path |

Trigger de sistema válido: `is_appropriate_planet_state_system` (no FE cluster, no home, no enclave, no `planet_state` ya, necesita planeta `uninhabitable_regular_planet` sin anomalía). El spawn **cambia** esa roca a city/nuked/gaia/etc.

---

## Ideas aparcadas (no mezclar en 2.0.0)

1. **Liberar colonia propia → city-state, vos te quedás el sistema.** Country type reutilizable (`set_owner` + estación). Diplomacia no. Es otro mod. Vanilla cercano: fundar enclave mercenario (estación, no planeta). Spawn Enclaves / Rebuild Enclaves = invitar estación a tu órbita, el planeta sigue tuyo.
2. **Estado nómada.** Encaja más con el original (lo *encontrás*). Opciones: convoy arkship (DLC Nomads), o estación tipo Champions Forge (sin DLC). No implementado.
3. **Más éticas / gestalts.** Mismo muro de diplomacia ×4.
4. **Colaborar con The24thDS.** Muerto. Bosmeri en mantenimiento. Publicar nosotros, crédito, página nueva.

---

## Publicar

- **Steam Workshop = canal real.** Paradox Mods (GOG / launcher PDX) es opcional si el launcher ofrece el checkbox; audiencia Stellaris irrelevante.
- Upload: launcher → Mods → Mod tools → Upload. Steam Cloud Sync ON. Primera vez **Hidden**. Actualizar: **Fetch Info** antes de Upload (si no, creás un item nuevo).
- Smoke **en Stellaris** antes de Public. Este puerto no se ha ejecutado in-game desde el sandbox.

### Smoke test rápido (no partida aleatoria)

Galaxia Tiny, 0 IA, 0 fallen/marauders/primitivos, hipervías máx. Ironman **off**. Playset solo este mod. Partida **nueva**.

Consola (`º` / `~`), nada seleccionado:

```
effect every_system = { limit = { has_star_flag = planet_state } set_name = "PLANET-STATE" } every_country = { limit = { is_country_type = planet_state } ROOT = { establish_communications = PREV } }
```

```
cash 20000
```

```
effect every_country = { limit = { is_country_type = planet_state } add_trust = { amount = 50 who = ROOT } }
```

Checklist: 4 sistemas PLANET-STATE + estación; Contactos → trato 10 del igualitario; pacto si hay trust; `create_starbase` outpost en `event_target:ps_egalitarian_planet`; guerra in-borders sí / fuera no.

No `event ps.1`. No `event ps.100` a mano (`From` queda mal). No Revived a la vez.

---

## Prompt para pegar en el otro chat

```
Leé https://github.com/Calrornds/planet_states
y el brief STELLARIS-4.4-MODDING.md (master).

Somos el puerto 4.4.6 de Planet States (v2.0.0).
No hagas un juego. No conviertas esto en un sandbox de vasallos.
No operes en el código salvo que lo pida explícito.

Hechos ya: PR #1 mergeado, release zip v2.0.0,
country type con destroys_starbases=no, spawn ×4,
diplomacia hardcoded a 4 flags.
Pendiente: smoke in-game, Workshop Hidden → Public.
Paradox Mods no es prioridad.
```
