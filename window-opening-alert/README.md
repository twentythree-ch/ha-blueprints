# Window Opening Alert

Automation blueprint that monitors an open window and sends tiered alerts based
on how long the window has been open, indoor temperature changes, and humidity
levels. Designed for use in heated rooms (e.g. bathroom) where leaving a window
open for too long causes unwanted heat loss.

Alerts are **suppressed** when the outdoor temperature is higher than the indoor
temperature (i.e. in summer / no heating loss expected).

## Import

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fwindow-opening-alert%2Fwindow_opening_alert.yaml)

## Required Sensors

| Sensor | Type | Example entity |
|--------|------|----------------|
| Window contact sensor | `binary_sensor` (device class `window`) | `binary_sensor.bad_eg_fenster_contact` |
| Indoor temperature | `sensor` (device class `temperature`) | `sensor.eg_bad_environmental_sensor_temperature` |
| Indoor humidity | `sensor` (device class `humidity`) | `sensor.eg_bad_environmental_sensor_humidity` |
| Outdoor temperature | `sensor` (device class `temperature`) | `sensor.outdoor_environmental_sensor_1_temperature` |

## Alert Levels

| Level | Urgency | Trigger conditions |
|-------|---------|--------------------|
| **1** | Informational | Humidity drops below the configured threshold (ventilation effective) **or** window has been open ≥ level 1 duration |
| **2** | Warning | Indoor temperature drops below the configured threshold **or** indoor temperature reaches outdoor temperature + offset |
| **3** | Critical | Indoor temperature drops to outdoor temperature **or** window has been open ≥ level 3 duration |

Each level has its own **Actions** input, so you can freely mix notification
services, light effects, Signal messages, etc.

## Inputs

### Sensors

| Input | Description |
|-------|-------------|
| **Window Sensor** | Binary sensor for the window (on = open) |
| **Indoor Temperature Sensor** | Temperature sensor inside the monitored room |
| **Indoor Humidity Sensor** | Humidity sensor inside the monitored room |
| **Outdoor Temperature Sensor** | Temperature sensor outside |

### Level 1 Thresholds

| Input | Default | Description |
|-------|---------|-------------|
| **Open Duration** | 15 min | Minutes the window must be open to fire a level 1 alert |
| **Humidity Normal Threshold** | 60 % | Humidity value (exclusive) below which level 1 is triggered |

### Level 2 Thresholds

| Input | Default | Description |
|-------|---------|-------------|
| **Indoor Temperature Threshold** | 19 °C | Indoor temperature (exclusive) below which level 2 is triggered |
| **Outdoor Temperature Offset** | 3 °C | Level 2 fires when indoor temp ≤ outdoor temp + this offset |

### Level 3 Thresholds

| Input | Default | Description |
|-------|---------|-------------|
| **Open Duration** | 30 min | Minutes the window must be open to fire a level 3 alert (must be > level 1 duration) |

### Actions

| Input | Description |
|-------|-------------|
| **Level 1 Alert Actions** | Actions to execute for informational alerts |
| **Level 2 Alert Actions** | Actions to execute for warning alerts |
| **Level 3 Alert Actions** | Actions to execute for critical alerts |

## Notes

- The automation runs in `single` mode; if an alert is already being processed
  a second trigger within the same instant is silently ignored.
- Temperature-based level 3 alerts (`temp_at_outdoor`) bypass the outdoor
  temperature guard because at that point the room has already cooled to outdoor
  temperature – the alert is always warranted.
- Set the level 3 open duration **higher** than the level 1 open duration to
  avoid duplicate alerts for the same event.
