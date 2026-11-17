# Weather-Compensated Heating Offset

**Current version: 1.0.0**

Adjusts a heating group offset (a `number` entity, for example the **Group
Offset** of a Climate Group helper) from the weather **forecast**. Room target
temperatures and presets are never touched.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fweather-compensated-offset%2Fweather_compensated_offset.yaml)

## How it works

Five minutes past every hour (and once at startup):

1. Read the **hourly** forecast of the selected weather entity.
2. Average the forecast temperature over a window, by default from +3 h to
   +12 h. The window starts in the future on purpose: underfloor heating
   reacts with a delay of several hours, so the weather *now* is the wrong
   input.
3. `offset = (reference temperature − forecast mean) × gain`
4. Round to the step of the offset entity, clamp to the configured limits, and
   move by at most *Maximum change per run* so the offset glides.
5. Write the offset only if it changed, and add a logbook entry.

If the forecast is unavailable or the window holds no data, the run stops and
the offset stays as it is.

## Inputs

| Input | Default | Purpose |
|-------|---------|---------|
| Weather entity | – | Weather entity with hourly forecasts (MeteoSwiss, met.no, …) |
| Offset entity | – | `number` entity that shifts all room targets |
| Reference outdoor temperature | 8 °C | Forecast mean at which the offset is 0 |
| Gain | 0.15 | °C offset per °C of deviation from the reference |
| Forecast window start / end | 3 h / 12 h | Averaging window, in hours from now |
| Lowest / highest allowed offset | −2 / +2 °C | Hard limits (also limited by the entity's own min/max) |
| Maximum change per run | 0.5 °C | Smoothing |
| Forecast log helper (optional) | – | `input_number` that receives the forecast mean every run |
| Additional conditions (optional) | – | e.g. skip while house mode is `vacation` |

Examples with the defaults: forecast mean 4 °C → +0.5 °C, 14 °C → −1.0 °C,
−10 °C → +2.0 °C (limit).

## Tuning

The defaults are a starting point, not a measured heating curve.

- Rooms too warm in mild weather: raise *Gain* or lower *Reference*.
- Rooms too cold when it is cold: raise *Gain* or *Highest allowed offset*.
- Offset reacts too late: move the window start earlier; too jumpy: widen the
  window.

## Checking forecast accuracy

Create an `input_number` helper (min −30, max 40, step 0.1, unit °C), select it
as *Forecast log helper* and compare its history with your outdoor sensor
about 3–12 hours later. Recorder keeps raw history for 10 days by default.

## Requirements

Home Assistant 2024.10 or newer. The weather entity must support the `hourly`
forecast type.

## Versioning

The version is shown in the blueprint name (`name:` in the YAML) and in the
first line of its description. It follows semantic versioning and must be
raised with every change to the YAML:

- **Patch** (1.0.x): bug fix, no change to inputs or behaviour.
- **Minor** (1.x.0): new input with a default, or behaviour change that keeps
  existing automations working.
- **Major** (x.0.0): input renamed or removed, which breaks existing
  automations.

## Changelog

### 1.0.0

- Initial release.
