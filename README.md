# ha-blueprints

A library of Home Assistant Blueprints for automating your smart home.

## Index

| Blueprint | Domain | Description |
|-----------|--------|-------------|
| [Window Opening Alert](#window-opening-alert) | automation | Tiered alerts when a window is open too long or causes indoor temperature / humidity changes. |
| [Bedtime Routine](#bedtime-routine) | script | Turns off lights, music, and devices, then checks whether windows, doors, and the garage are closed and sends tiered security alerts. |
| [Garage Door Alert](#garage-door-alert) | automation | Notifies selected users when the garage door has been open too long; automatically dismisses the alert when the door closes. |
| [Weather-Compensated Heating Offset](#weather-compensated-heating-offset) | automation | Hourly adjusts a heating group offset from the weather forecast, without touching room target temperatures. |

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

### [Bedtime Routine](./bedtime-routine/)

A manually triggered script that turns off lights, music, and devices, then
checks whether windows, doors, and the garage are closed and sends tiered
alerts accordingly. Trigger it from the Home Assistant Companion app, a
dashboard button, or an iOS / Android shortcut.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fbedtime-routine%2Fbedtime_routine.yaml)

**Built-in turn-off steps:** lights · media players · switches / smart plugs
(each with its own entity selector; leave empty to skip)

**House-state sensors:** window sensors · door sensors · garage door sensors

| Alert level | Condition | Colour |
|-------------|-----------|--------|
| 1 – All Good | All windows, doors, and garage closed | 🟢 Green |
| 2 – Windows Open | One or more window sensors report open | 🟠 Orange |
| 3 – Door / Garage Open | A door or garage door is open | 🔴 Red |

Level 2 and Level 3 can both trigger in the same run. Level 1 only runs when
neither Level 2 nor Level 3 is triggered. Each level has fully configurable
actions (HA notifications, Signal messages, scenes, …).

---

### [Garage Door Alert](./garage-door-alert/)

Monitors a garage door sensor and sends a notification when the door has been
open for longer than a configurable duration. The alert is automatically
dismissed on all selected devices when the door closes, and replaced by a
"door closed" confirmation message. Supports any number of Home Assistant
Companion App devices as notification targets.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fgarage-door-alert%2Fgarage_door_alert.yaml)

**Sensor required:** garage door contact sensor

| Event | Condition | Result |
|-------|-----------|--------|
| Door opened | Stays open ≥ configured duration | Persistent HA notification · Tagged push notification to all selected devices |
| Door closed | Was open ≥ configured duration | Persistent notification dismissed · "Closed" push notification replaces open alert |

---

### [Weather-Compensated Heating Offset](./weather-compensated-offset/)

Adjusts a heating group offset (a `number` entity, e.g. the Group Offset of a
Climate Group helper) from the hourly weather forecast. The averaging window
lies a few hours ahead to account for the delay of underfloor heating. Room
targets and presets are left alone; the offset is clamped and changes in small
steps.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fweather-compensated-offset%2Fweather_compensated_offset.yaml)

**Entities required:** weather entity with hourly forecast · offset `number`
entity

---

## Usage

1. Click the **Import Blueprint** badge on any blueprint above.
2. Alternatively, go to **Settings → Automations & Scenes → Blueprints** in
   Home Assistant and use the **Import Blueprint** button, pasting the raw URL
   of the `.yaml` file.
3. Create one or more automations from the imported blueprint and configure the
   inputs to match your sensors and preferred alert actions.
