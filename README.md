# ha-blueprints

Home Assistant automation blueprints.

## Available blueprints

- [`lights_off_no_motion.yaml`](lights_off_no_motion.yaml) — Dim and turn off lights after a configurable period of no motion, with a grace period and motion cancellation.
- [`spa_heater_mode.yaml`](spa_heater_mode.yaml) — Control a ControlMySpa-style heater mode select based on electricity price and a force-heating toggle. Includes a `READY_REST` watchdog and periodic drift correction. Each behavior is individually toggleable.
- [`spa_temperature_freshness.yaml`](spa_temperature_freshness.yaml) — Alert when the spa temperature sensor hasn't updated for a configurable duration, and optionally pulse the jet pump to force fresh data.
- [`spa_time_sync.yaml`](spa_time_sync.yaml) — Once-daily check that compares the spa's clock to Home Assistant time and pushes a time update if drift exceeds a threshold (handles DST and RTC drift).
- [`spa_api_stale_alert.yaml`](spa_api_stale_alert.yaml) — Notify when the spa integration's API stops returning fresh data while the spa itself remains online.
