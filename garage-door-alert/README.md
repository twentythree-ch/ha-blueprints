# Garage Door Alert

Automation blueprint that sends a push notification and a persistent Home
Assistant notification when the garage door has been open for more than a
configurable number of minutes. When the door closes, the open notification is
automatically dismissed on all selected devices and replaced by a "door closed"
confirmation.

## Import

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fgarage-door-alert%2Fgarage_door_alert.yaml)

## Required Sensor

| Sensor | Type | Example entity |
|--------|------|----------------|
| Garage door contact sensor | `binary_sensor` (device class `garage_door`) | `binary_sensor.garage_door_contact` |

## Behaviour

| Trigger | Condition | Action |
|---------|-----------|--------|
| Garage door opens | Door stays open ≥ **Open Duration** minutes | Create persistent HA notification · Send tagged push notification to all selected devices |
| Garage door closes | Door was previously open ≥ **Open Duration** minutes | Dismiss persistent HA notification · Send "closed" push notification with the same tag (replaces the open notification on device) |

The "door closed" sequence only runs when the door was actually open long enough
to have triggered the open notification. Brief openings (shorter than the
configured duration) produce no notifications at all.

## Inputs

| Input | Default | Description |
|-------|---------|-------------|
| **Garage Door Sensor** | – | Binary sensor for the garage door (`on` = open, `off` = closed) |
| **Open Duration** | 10 min | Minutes the door must be continuously open before the alert fires |
| **Open Notification Message** | `Garage door is open` | Text of the notification sent when the door has been open too long |
| **Closed Notification Message** | `Garage door has been closed` | Text of the notification sent when the door closes (replaces the open notification) |
| **Notification ID** | `garage_door_notification` | Unique tag used to match and dismiss notifications across all targets; change this when running multiple instances |
| **Notification Targets** | *(empty)* | One or more Home Assistant Companion App devices to notify; leave empty to skip mobile notifications |

## Notes

- Notifications sent to mobile devices use a **tag** equal to the
  *Notification ID*. On both Android and iOS the "door closed" message replaces
  the "door open" message in the notification drawer automatically.
- The persistent HA notification (visible in the sidebar) is created with the
  same *Notification ID* and is explicitly dismissed when the door closes.
- If you have multiple garage doors, create one automation per door and set a
  **different Notification ID** for each so that their notifications don't
  interfere with each other.
- The blueprint runs in `queued` mode (max 5 concurrent runs) so that a
  rapid open/close cycle is still handled correctly.
