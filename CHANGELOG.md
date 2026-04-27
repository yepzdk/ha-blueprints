# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- `spa_heater_mode.yaml` — blueprint combining seven spa heater-mode automations (price-based READY/REST, force-heating override, `READY_REST` watchdog, periodic drift assertion) into one blueprint with per-behavior toggles.
- `spa_temperature_freshness.yaml` — blueprint combining stale-temperature alerting and jet-pump force-refresh into one blueprint with independent toggles.
- `spa_time_sync.yaml` — blueprint for daily spa clock drift check and resync.
- `spa_api_stale_alert.yaml` — blueprint for alerting when the spa integration's API stops returning fresh data while the spa is still online.
- README list of available blueprints.
- README Prerequisites section documenting external dependencies (ControlMySpa integration, price sensor, helpers, template sensors for API staleness, notify target) with example YAML.
- Per-event notification opt-in toggles (default off): `notify_on_watchdog`, `notify_on_assert_correction` in `spa_heater_mode`; `notify_on_stale`, `notify_on_force_refresh` in `spa_temperature_freshness`; `notify_on_sync` in `spa_time_sync`. Notifications are no longer sent automatically just because `notify_service` is set.

### Changed

- `spa_heater_mode.yaml`: replaced numeric `assert_interval_minutes` with cron-style text input `assert_interval` (default `"/15"`). The previous numeric default of `15` would only fire once per hour at minute 15, not every 15 minutes.
- `spa_heater_mode.yaml`: switched to `mode: queued` with `max: 3` so concurrent triggers (e.g. cheap-hour transition mid-assertion) are not dropped. Actions are idempotent so reordering is safe.
- `spa_heater_mode.yaml`: gated each trigger with the corresponding `enabled:` flag (HA 2024.4+) so disabled behaviors don't evaluate triggers at all.
- `spa_temperature_freshness.yaml`: triggers now ignore `unknown` and `unavailable` states via `not_to:`, preventing spurious alerts after integration restarts.
- `spa_temperature_freshness.yaml`: gated each trigger with `enabled:` for `enable_stale_alert` / `enable_force_refresh`.
- `spa_time_sync.yaml`: `dismiss_notification_id` now defaults to blank (was `control_my_spa_time_update`). The default was integration-specific and didn't belong as a built-in default; ControlMySpa users can still set the value explicitly.
- `spa_api_stale_alert.yaml`: `minutes_since_update_sensor` is now a text input (was an entity selector with an empty-string default, which HA does not accept).
- All blueprints: documented that `notify_service` expects a service id (`notify.<name>`), not a friendly device name. Documented hardcoded `READY` / `REST` / `READY_REST` literals in `spa_heater_mode` so users with differently named select options know to fork.
