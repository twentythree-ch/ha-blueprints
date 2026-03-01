# Miele Appliance Finished

Automation blueprint that sends notifications and triggers configurable actions
when a Miele washing machine or tumble dryer finishes its program. Requires the
official **Miele integration** included in Home Assistant Core.

## Import

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fmiele-appliance-finished%2Fmiele_appliance_finished.yaml)

## Required Sensor

| Sensor | Type | Example entity |
|--------|------|----------------|
| Miele appliance state sensor | `sensor` | `sensor.washing_machine_state` |

The sensor is created automatically by the Miele integration for each appliance.

## Behaviour

| Trigger | Condition | Action |
|---------|-----------|--------|
| Appliance state → *"End of program"* | — | Create persistent HA notification · Send tagged push notification to all selected devices (home-zone check applied) · Turn on alert light · Run custom actions |
| Appliance state leaves *"End of program"* | — | Dismiss persistent HA notification · Turn off alert light · Run reset actions |

Push notifications are only sent when **at least one of the configured persons
is in the specified home zone**. If no persons are configured the push
notification is sent unconditionally (as long as notification targets are
selected).

## Inputs

### Appliance

| Input | Default | Description |
|-------|---------|-------------|
| **Appliance State Sensor** | – | The Miele state sensor to watch |
| **"Program Ended" State Value** | `End of program` | Sensor state string that means the program has finished; adjust for non-English HA installations |

### Notifications

| Input | Default | Description |
|-------|---------|-------------|
| **Notification Title** | `Appliance finished` | Title of the persistent and push notifications |
| **Notification Message** | `Your appliance has finished its program.` | Body of the persistent and push notifications |
| **Notification ID** | `miele_appliance_finished` | Unique tag used to match and dismiss notifications; change when running multiple instances |
| **Companion App Notification Targets** | *(empty)* | One or more Companion App devices to notify; leave empty to skip mobile notifications |

### Home Presence

| Input | Default | Description |
|-------|---------|-------------|
| **Persons to Check** | *(empty)* | Person entities used for the at-home check; leave empty to skip the check |
| **Home Zone** | `zone.home` | Zone that counts as "at home" |

### Alert Light

| Input | Default | Description |
|-------|---------|-------------|
| **Alert Light** | *(empty)* | Light or light group to turn on; leave empty to skip |
| **Alert Light Brightness** | `100 %` | Brightness (1–100 %) |
| **Enable Alert Light Color** | `false` | Apply a specific RGB color |
| **Alert Light Color** | `[0, 255, 0]` (green) | RGB color used when color is enabled |
| **Alert Light Effect** | *(empty)* | Effect name (e.g. `colorloop`, `pulse`); leave empty for none |
| **Alert Light Transition** | `1 s` | Fade time when turning the light on and off |

### Custom Actions

| Input | Default | Description |
|-------|---------|-------------|
| **Program Ended – Additional Actions** | *(empty)* | Extra actions when the program ends (e.g. play a chime, send a Signal message) |
| **Program Ended State Left – Additional Actions** | *(empty)* | Extra actions when the appliance leaves the "program ended" state |

## Notes

- The **persistent HA notification** (visible in the sidebar) is created with
  the *Notification ID* as its key and is explicitly dismissed when the
  appliance leaves the "program ended" state.
- Push notifications sent to mobile devices carry a **tag** equal to the
  *Notification ID*, so the notification is properly grouped on the device.
- The **"Program Ended" State Value** is language-dependent. Open your HA
  Developer Tools → States, find the Miele sensor, finish a cycle, and note
  the exact string that appears if the default `End of program` does not work.
- Create **one automation per appliance** and use a **different Notification
  ID** for each so notifications do not interfere with each other.
- The blueprint runs in `queued` mode (max 5 concurrent runs) to handle edge
  cases such as a rapid state change during HA startup.
