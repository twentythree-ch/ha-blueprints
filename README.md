# ha-blueprints

A library of Home Assistant Blueprints for automating your smart home.

## Blueprints

### [Window Opening Alert](./window-opening-alert/)

Monitors a window sensor and sends tiered alerts when the window is open too
long, the indoor temperature drops, or humidity returns to normal. Ideal for
heated rooms such as a bathroom where excessive ventilation causes unwanted heat
loss.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fwindow-opening-alert%2Fwindow_opening_alert.yaml)

**Sensors required:** window contact sensor · indoor temperature · indoor
humidity · outdoor temperature

| Alert level | Triggers |
|-------------|----------|
| 1 – Informational | Humidity back to normal · Window open ≥ level 1 duration |
| 2 – Warning | Indoor temperature drops below threshold · Indoor temp reaches outdoor + offset |
| 3 – Critical | Indoor temperature at outdoor level · Window open ≥ level 3 duration |

Each level has fully configurable actions (notifications, light flashes, Signal
messages, …). Alerts are suppressed when the outdoor temperature is higher than
the indoor temperature.

---

## Usage

1. Click the **Import Blueprint** badge on any blueprint above.
2. Alternatively, go to **Settings → Automations & Scenes → Blueprints** in
   Home Assistant and use the **Import Blueprint** button, pasting the raw URL
   of the `.yaml` file.
3. Create one or more automations from the imported blueprint and configure the
   inputs to match your sensors and preferred alert actions.
