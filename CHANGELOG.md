# Changelog

## 2026-05-01

- Reformatted the Lovelace YAML so it is readable and easier to diff.
- Replaced the duplicated conditional Pause / Play / Stop / Power blocks with single `custom:button-card` controls that compute active/ghosted state in JavaScript styles.
- Added confirmation prompts to Stop and Power actions.
- Kept the filename display as the existing `state-label` implementation.
- Added optional HACS/custom-template metadata files: `hacs.json`, `info.md`, and `elegoo_saturn_ultra_card.jinja`.
- Added rollback/update instructions in `docs/UPDATE_AND_ROLLBACK.md`.

## 2025-08-16

- UV Temp chip always visible with `—` when off/unavailable.
- Remaining time rendered as `Hh Mm`.
- Filename clamped with ellipsis.
- Optional power icon via `switch.<base>_power`.
- Off-state defaults for status, percent, layers, and time.

## 2025-08-15

- Controls always visible.
- State-based ghosting for Pause, Play, and Stop.
- Unified text glow and icon sizes.
