# ha-blueprints

Home Assistant automation blueprints.

## Available blueprints

- [`lights_off_no_motion.yaml`](lights_off_no_motion.yaml) — Dim and turn off lights after a configurable period of no motion, with a grace period and motion cancellation.
- [`spa_heater_mode.yaml`](spa_heater_mode.yaml) — Control a ControlMySpa-style heater mode select based on electricity price and a force-heating toggle. Includes a `READY_REST` watchdog and periodic drift correction. Each behavior is individually toggleable.
- [`spa_temperature_freshness.yaml`](spa_temperature_freshness.yaml) — Alert when the spa temperature sensor hasn't updated for a configurable duration, and optionally pulse the jet pump to force fresh data.
- [`spa_time_sync.yaml`](spa_time_sync.yaml) — Once-daily check that compares the spa's clock to Home Assistant time and pushes a time update if drift exceeds a threshold (handles DST and RTC drift).
- [`spa_api_stale_alert.yaml`](spa_api_stale_alert.yaml) — Notify when the spa integration's API stops returning fresh data while the spa itself remains online.

## Prerequisites

The spa blueprints don't hard-depend on any particular integration — they accept generic entity types — but they assume a number of entities already exist in your setup. The list below documents what you need to have configured before instantiating them.

### For all spa blueprints

- **ControlMySpa integration** (HACS custom component) providing:
  - `select.spa_heater_mode` with options `READY`, `REST`, `READY_REST`
  - `binary_sensor.isonline` (or similar reachability signal) — used as the online gate
  - `sensor.spa_current_temperature`
  - `switch.spa_pump`
  - `button.spa_update_time`
  - `sensor.spa_clock` (reports spa clock as `HH:MM`)

  Any integration exposing equivalent entities works — the blueprints only require the entity types, not specific names.

- **Notification target** — e.g. `notify.mobile_app_<device>` from the Home Assistant Companion App, or any other notify service. All notifications are optional; leave the `notify_service` input blank to disable them.

### For `spa_heater_mode.yaml`

- **Cheap-hour binary sensor** — a `binary_sensor` that is `on` during the hours you want the heater in READY mode. Typically a template sensor built on top of an electricity price integration (Nord Pool, ENTSO-e, Energi Data Service, etc.). Example:

  ```yaml
  # config/templates/spa.yaml
  template:
    - binary_sensor:
        - name: "Spa cheap heating hour"
          state: >
            {{ states('sensor.nordpool_kwh_dk1_eur_3_10_025') | float(99)
               < states('input_number.spa_price_threshold') | float(0.5) }}
  ```

- **Force-heating helper** — an `input_boolean` used to force READY mode regardless of price:

  ```yaml
  # configuration.yaml
  input_boolean:
    spa_force_heating:
      name: Spa force heating
      icon: mdi:hot-tub
  ```

### For `spa_api_stale_alert.yaml`

- **API-stale binary sensor** and **minutes-since-update sensor** — template sensors derived from the last-reported timestamp of the ControlMySpa temperature entity. Example:

  ```yaml
  # config/templates/spa.yaml
  template:
    - binary_sensor:
        - name: "Spa API stale"
          state: >
            {{ (now() - states.sensor.spa_current_temperature.last_reported).total_seconds() > 900 }}
    - sensor:
        - name: "Spa API minutes since update"
          unit_of_measurement: min
          state: >
            {{ ((now() - states.sensor.spa_current_temperature.last_reported).total_seconds() / 60) | round(0) }}
  ```

  Adjust the 900-second threshold (15 min) to taste.

### For `spa_time_sync.yaml`

- Nothing beyond the ControlMySpa entities listed above. The blueprint optionally dismisses a persistent notification by ID after pressing the update-time button; the default ID matches what ControlMySpa raises, but it can be overridden or left blank.
