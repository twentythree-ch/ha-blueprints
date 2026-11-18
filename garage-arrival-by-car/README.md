# Garage Arrival by Car

Automation blueprint that opens the garage door when you approach home **by
car**, and never when you arrive on foot. "In the car" is detected by your
phone being connected to the car (Android Auto, or CarPlay via a shortcut);
the approach is detected by your phone entering a zone around your home.

## Import

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftwentythree-ch%2Fha-blueprints%2Fblob%2Fmain%2Fgarage-arrival-by-car%2Fgarage_arrival_by_car.yaml)

## Required Entities

| Entity | Type | Example entity |
|--------|------|----------------|
| Phone location | `device_tracker` (Companion App) | `device_tracker.my_phone` |
| In-car sensor | `binary_sensor` or `input_boolean` | `binary_sensor.my_phone_android_auto` |
| Garage door | `cover` | `cover.garage_door` |
| Approach zone | `zone` | `zone.home_approach` |

## Behaviour

| Trigger | Conditions | Action |
|---------|------------|--------|
| Phone enters the **Approach Zone** | In-car sensor is `on` · Garage door is `closed` · Phone's location state unchanged for ≥ **Minimum Time Away** | Open the garage door · Optional push notification |

| Situation | Result |
|-----------|--------|
| Driving home | Door opens when you enter the approach zone |
| Walking home | Nothing (in-car sensor is off) |
| Driving away | Nothing: you are already inside the approach zone when you leave, so there is no "enter" event; GPS jitter at the zone edge is blocked by *Minimum Time Away* |
| Door already open / moving | Nothing |

## Inputs

| Input | Default | Description |
|-------|---------|-------------|
| **Phone Location Tracker** | – | GPS device tracker of the phone (Companion App) |
| **In-Car Sensor** | – | On while the phone is connected to the car |
| **Garage Door** | – | Cover to open |
| **Approach Zone** | – | Zone whose entry triggers the opening |
| **Minimum Time Away** | 5 min | Location state must have been unchanged this long before the zone entry counts |
| **Notification Targets** | *(empty)* | Companion App devices to notify when the door opens |
| **Opened Notification Message** | `Garage opened – welcome home` | Notification text |

## Setup

1. **Approach zone.** In **Settings → Areas, labels & zones → Zones**, create a
   zone centred on your home with a radius of roughly 300–500 m (larger if you
   arrive at higher speed). Tick **Passive** so it doesn't change your
   `home`/`not_home` state; the zone trigger works on coordinates, so passive
   zones still trigger. Don't use the Home zone itself: it is small and,
   with sparse GPS updates, often entered only when you are already at the door.
2. **In-car sensor.**
   - *Android:* in the Companion App, **Settings → Companion app → Manage
     sensors → Android Auto**, enable the sensor.
   - *iOS:* create an `input_boolean` (e.g. `input_boolean.in_car`) and two
     Shortcuts personal automations: "When CarPlay connects" → turn it on,
     "When CarPlay disconnects" → turn it off.
   - Alternatively, a template binary sensor based on the Bluetooth connection
     sensor (car's Bluetooth device in `connected_paired_devices`).
3. **Location accuracy (important).** Phones throttle GPS updates, which can
   make the trigger late. On Android, in **Manage sensors → Background
   location**, enable **High accuracy mode** and set its trigger to the
   Android Auto connection (or the car's Bluetooth device), and/or set the
   **High accuracy mode trigger range for zone** for your approach zone.

## Notes

- Passengers: if your phone connects to Android Auto in someone else's car,
  the door opens too.
- Security: the trust model equals that of your phone. Anyone who has your
  phone and connects it to a car near your home opens the garage.
- After a Home Assistant restart, the tracker's `last_changed` is reset, so
  for *Minimum Time Away* minutes after the restart no opening happens.
- The blueprint runs in `single` mode; further zone entries while a run is
  in progress are ignored.
