# Changelog

## 2.0.1 — Stellaris 4.4.6

- Nomad empires can use the in-borders hostility rule against Planet-States (vanilla `is_nomadic` branch no longer blocks them entirely).
- First-contact sounds cover Aquatic portraits.

## 2.0.0 — Stellaris 4.4.6 (Pegasus)

Port from the 2019 original / 3.4 The24thDS fork to live Stellaris.

### Gameplay kept

- Four Planet-States at game start (Egalitarian, Authoritarian, Militarist, Pacifist)
- Resource deals 10–50 / month and Commercial Pacts at 50 trust
- In-borders-only hostility
- Planet-State Station defense
- First-contact stages

### Engine / 4.x fixes

- `supported_version="v4.4.*"`
- `create_pop_group` (pops ×100) instead of `create_pop`
- Rulers are `official` leaders (Galactic Paragons class system)
- Hidden `origin_planet_state` so random origins cannot attach
- Buildings declare `building_sets`; job/housing/amenities scaled for workforce
- `can_take_hostile_actions` updated for Nomads (`is_nomadic`)
- `destroys_starbases = no` so you can actually park a starbase in their system (as the original README promised)
- Removed `frontend.gui` overwrite
- Commercial pact modifiers also apply `country_trade_produces_mult`
- Localisation moved under `localisation/english/` and `localisation/spanish/`
- First contact covers Toxoids

## 1.6.2 — Stellaris 3.4.4 (The24thDS)

Upstream maintenance. See The24thDS/planet_states.
