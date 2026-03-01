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
| Appliance state → *"End of program"* | — | Create persistent HA notification · Send tagged push notification to each person who is currently in the home zone (only their own device(s)) · Turn on alert light · Run custom actions |
| Appliance state leaves *"End of program"* | — | Dismiss persistent HA notification · Turn off alert light · Run reset actions |

Each person is paired with their own Companion App device(s). A push
notification is sent **only to persons who are currently in the home zone**.
If Person A and Person B are configured but only Person B is home, only
Person B's device(s) receive the notification. Up to 5 person → device pairs
can be configured.

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

### Home Zone & Person → Device Pairs

| Input | Default | Description |
|-------|---------|-------------|
| **Home Zone** | `zone.home` | Zone that counts as "at home" for all presence checks |
| **Person 1** | *(empty)* | First person entity to check |
| **Person 1 – Notification Device(s)** | *(empty)* | Companion App device(s) for Person 1; only notified when Person 1 is home |
| **Person 2 (optional)** | *(empty)* | Second person entity to check |
| **Person 2 – Notification Device(s)** | *(empty)* | Companion App device(s) for Person 2 |
| **Person 3 (optional)** | *(empty)* | Third person entity to check |
| **Person 3 – Notification Device(s)** | *(empty)* | Companion App device(s) for Person 3 |
| **Person 4 (optional)** | *(empty)* | Fourth person entity to check |
| **Person 4 – Notification Device(s)** | *(empty)* | Companion App device(s) for Person 4 |
| **Person 5 (optional)** | *(empty)* | Fifth person entity to check |
| **Person 5 – Notification Device(s)** | *(empty)* | Companion App device(s) for Person 5 |

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

- Push notifications are sent **per-person**: only persons currently in the
  home zone receive a notification, and only to their own paired device(s).
  Example: if Person A and B are configured but only B is home, only B's
  device is notified.
- Up to **5 person → device pairs** can be configured. Leave unused slots
  empty.
- Push notifications sent to mobile devices carry a **tag** equal to the
  *Notification ID*, so the notification is properly grouped on the device.
- The **"Program Ended" State Value** is language-dependent. Open your HA
  Developer Tools → States, find the Miele sensor, finish a cycle, and note
  the exact string that appears if the default `End of program` does not work.
- Create **one automation per appliance** and use a **different Notification
  ID** for each so notifications do not interfere with each other.
- The blueprint runs in `queued` mode (max 5 concurrent runs) to handle edge
  cases such as a rapid state change during HA startup.
