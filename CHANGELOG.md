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
