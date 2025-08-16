# Elegoo Saturn 4 Ultra – Lovelace Card

A Home Assistant Lovelace card that presents a clean, high-contrast status panel for Elegoo resin printers (tested with the Saturn 4 Ultra). It auto-discovers printers from the `elegoo_printer` integration and renders one **picture-elements** panel per device, complete with live chamber camera, progress, layer info, UV LED temperature, error reason, and quick controls (pause/resume/stop).

![Card background](assets/saturn_4_ultra.png)

## Features
- Works with multiple printers discovered from the `elegoo_printer` integration.
- Live camera tile (center) using `camera.<base>_chamber_camera`.
- Key stats: current status, percent complete, remaining time (**rendered as `Hh Mm` from minute sensor**), current/total layers.
- **UV LED temperature chip (always visible):** shows `—` when the printer is off or the value is warming up/unknown; displays **one decimal place** when available.
- **Controls always visible**: Pause / Play / Stop stay in place and switch between **active** and **ghosted** based on print status.
- Shows current file name (strips path and extension; **clamped with ellipsis** for long names).
- Highlights error reason when reported.
- Uses a background PNG for a polished look.
- *(Optional)* **Power button**: if a plug named `switch.<base>_power` exists, a power icon appears (clickable when Off/Idle/Stopped/Complete; ghosted during active printing).

## Controls: always visible (active vs. ghosted)

The Pause / Play / Stop icons are always visible. They switch between **active** (clickable) and **ghosted** (dimmed, non-clickable) using `sensor.<base>_print_status`.

| Status                                                                 | Pause | Play | Stop | Notes                         |
|------------------------------------------------------------------------|:-----:|:----:|:----:|--------------------------------|
| `printing`, `homing`, `dropping`, `lifting`, `file_checking`, `recovery`, `loading` | **Active** | Ghost | **Active** | Busy/in-progress states       |
| `paused`                                                               | Ghost | **Active** | **Active** | Resume or stop                |
| `pausing`                                                              | Ghost | Ghost | **Active** | Transitioning; only Stop stays active |
| `stopping`                                                             | Ghost | Ghost | Ghost | Already stopping              |
| `idle`, `complete`, `stopped`                                          | Ghost | Ghost | Ghost | Nothing actionable            |

> “Ghosted” = visible but disabled (`pointer-events: none`) and dimmed.

**How it works (high-level)**  
- **Pause** is active when status is **not** one of: `idle`, `complete`, `stopped`, `paused`, `pausing`, `stopping`.  
- **Play** is active **only** when `paused`.  
- **Stop** is active when status is **not** one of: `idle`, `complete`, `stopped`, `stopping`.

**Customize the behavior**  
Prefer Pause to stay active during `pausing`? Remove `pausing` from Pause’s “not in” list. Want Stop active during `stopping`? Remove `stopping` from Stop’s “not in” list. (Search the Lovelace YAML for `'_print_status'` and edit the `conditional` blocks’ `state`/`state_not` values.)

**Theming & ghost color**  
Ghost icons are dimmed with either:
- `opacity: 0.5` + `color: white` (default), or  
- `color: rgba(255,255,255,0.55)` and no `opacity` (more consistent across themes/backgrounds).

## Requirements
- **Home Assistant** with a working [**Elegoo printer** integration](https://github.com/danielcherubini/elegoo-homeassistant) exposing entities like:
  - `sensor.<base>_current_status` **or** `sensor.<base>_print_status` (anchor)
  - `sensor.<base>_percent_complete`
  - `sensor.<base>_remaining_print_time`
  - `sensor.<base>_current_layer`, `sensor.<base>_total_layers`
  - `sensor.<base>_file_name`, `sensor.<base>_error_status_reason`
  - `sensor.<base>_uv_led_temp`
  - `camera.<base>_chamber_camera`
  - `button.<base>_pause_print`, `button.<base>_resume_print`, `button.<base>_stop_print`
- Lovelace custom cards (install via HACS):
  - [`auto-entities`](https://github.com/thomasloven/lovelace-auto-entities)
  - [`layout-card`](https://github.com/thomasloven/lovelace-layout-card)
  - [`button-card`](https://github.com/custom-cards/button-card)
- *(Optional)* Smart plug named `switch.<base>_power` to enable the power icon.

## Installation
1. Copy **assets/saturn_4_ultra.png** into your Home Assistant `/config/www/` folder.
   - After copying, it will be available in Lovelace as: `/local/saturn_4_ultra.png` (already referenced in the YAML).
2. Add the YAML from `lovelace/elegoo_saturn_ultra_card.yaml` to your dashboard:
   - *UI mode*: **Edit dashboard → Add card → Manual** and paste the YAML.
   - *YAML mode*: include the file or paste into your view’s cards list.
3. Confirm your Elegoo entities exist and follow the `<base>_…` pattern used above. The card auto-generates one panel per device by detecting an “anchor” sensor ending in `_current_status` (or `_print_status` as a fallback).

## Customization
- Replace the background at `image: '/local/saturn_4_ultra.png'` if you prefer a different art style.
- Tweak element positions by editing the `style.top/left` percentages.
- **Filename length**: adjust the clamp by changing the `width/max-width` (e.g., `15ch`) in the filename label styles.
- If you want to show only a specific printer, wrap the card with `auto-entities` filters or fork the template to hard-set `base` to the device you want.

## Troubleshooting
- **Card renders blank**: Usually means no anchor sensor was found. Ensure either `sensor.<base>_current_status` or `sensor.<base>_print_status` exists for your printer and the integration is loaded.
- **No background image**: Verify the file is at `/config/www/saturn_4_ultra.png` and the Lovelace path `/local/saturn_4_ultra.png` is correct. Clear browser cache/hard-refresh.
- **Buttons do nothing**: Confirm you have the `button.*` entities and that your account has permission to call `button.press`.
- **Power icon not visible**: Ensure your plug entity is named `switch.<base>_power`. The icon is clickable when the plug is OFF, and when ON in safe states (`idle`, `stopped`, `complete`); it is ghosted during active printing.
- **Sensors show “unavailable” right after turning on**: The card masks these with sensible defaults:
  - UV Temp → `—`
  - Remaining time → `—` (until minutes are reported; then shows `Hh Mm`)
  - Status → **Off**, Percent → **0%**, Layer/Total → **0** when plug is off

## Example YAML
See [`lovelace/elegoo_saturn_ultra_card.yaml`](lovelace/elegoo_saturn_ultra_card.yaml) for the full, ready-to-paste configuration (the same content you see in this repository).

## Examples
| Active controls | Idle controls |
| --- | --- |
| <img src="assets/card_example_active.png" width="420" alt="Active controls"> | <img src="assets/card_example_idle.png" width="420" alt="Idle controls"> |

## Folder Structure
```
elegoo-saturn-ultra-card/
├─ assets/
│ └─ saturn_4_ultra.png
├─ lovelace/
│ └─ elegoo_saturn_ultra_card.yaml
├─ .gitignore
├─ LICENSE
└─ README.md

```


## License
MIT — see [`LICENSE`](LICENSE).

## Credits
Art and YAML template by the project author. Not affiliated with Elegoo.

---

## Changelog
- **2025-08-16** — UV Temp chip always visible with `—` when off/unavailable; one-decimal formatting. Remaining time now rendered as `Hh Mm`. Filename clamped with ellipsis. Optional power icon via `switch.<base>_power`. Off-state defaults for status/percent/layers/time.
- **2025-08-15** — Controls now always visible; state-based ghosting for Pause/Play/Stop; unified text glow and icon sizes.
