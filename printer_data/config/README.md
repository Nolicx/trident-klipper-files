# Voron Trident 300mm — Klipper Config

Klipper configuration for a Voron Trident 300mm CoreXY printer.
Backup provided by [Klipper-Backup](https://github.com/Staubgeborener/Klipper-Backup).

---

## Klipper Extensions

### LEDs & Visuals

| Extension | Description |
|---|---|
| [klipper-led_effect](https://github.com/julianschill/klipper-led_effect) | Enables animated LED effects (breathing, gradient, twinkle, etc.) on NeoPixel strips. Used for all StealthBurner and Disco on a Stick status effects. |
| [klipper-virtual_leds](https://github.com/MapleLeafMakers/klipper-virtual_leds) | Allows grouping and aliasing of LED segments as virtual LEDs, making it easier to control individual parts of a strip independently. |

### Motor Tuning

| Extension | Description |
|---|---|
| [klipper_tmc_autotune](https://github.com/andrewmcgr/klipper_tmc_autotune) | Automatically tunes TMC stepper driver parameters (current, StealthChop thresholds, etc.) based on motor specs and a defined tuning goal. Replaces manual driver configuration. |
| [chopper-resonance-tuner](https://github.com/MRX8024/chopper-resonance-tuner) | Helps tune TMC chopper settings to reduce stepper motor resonance and noise at specific speeds. |

### Calibration & Input Shaping

| Extension | Description |
|---|---|
| [klippain-shaketune](https://github.com/Frix-x/klippain-shaketune) | Extended input shaper toolset. Runs resonance measurements via ADXL345 and generates detailed analysis graphs for tuning input shaping, belt tension, and vibration damping. |

### Bed Meshing & Purging

| Extension | Description |
|---|---|
| [Klipper-Adaptive-Meshing-Purging (KAMP)](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging) | Generates a bed mesh and purge line only in the area actually used by the print. Reduces print start time and improves first layer accuracy for small prints. |

---

## Macros

### Print Flow

| Macro | File | Description |
|---|---|---|
| `PRINT_START` | `macros/print_start.cfg` | Full print start sequence: homes axes, heats bed with optional heatsoak, heats nozzle to probe temp, cleans nozzle, homes Z, levels gantry, cleans again, homes Z, maps bed mesh, heats to print temp, final clean, purge line. |
| `PRINT_END` | `macros/print_end.cfg` | Print end sequence: retracts filament, raises Z, cleans nozzle, turns off heaters and fans, lowers bed to presentation height, disables motors. |

### Toolhead

| Macro | File | Description |
|---|---|---|
| `PARK` | `macros/park.cfg` | Moves the toolhead safely to the brush/park position at the rear of the bed. Raises Z first, then navigates with a safe approach to avoid collisions near the rear. Homes axes if needed. |
| `CLEAN_NOZZLE` | `macros/clean_nozzle.cfg` | Cleans the nozzle on the brush. Supports a linear wipe mode and a circular arc scrub mode, configurable via `_VARIABLES`. Optionally purges filament before wiping. Parameters: `PURGE=` (mm to purge), `CLEAN=` (target temp °C), `RETURN=` (raise Z after cleaning, default 1). |

### Homing

| Macro | File | Description |
|---|---|---|
| `CG28` | `macros/misc.cfg` | Conditional home — homes all axes only if not already homed. |
| `CG28XY` | `macros/misc.cfg` | Conditional home — homes X and Y only if not already homed. |
| `CG28Z` | `macros/misc.cfg` | Conditional home — homes Z only if not already homed. |
| `Z_TILT_ADJUST` | `macros/misc.cfg` | Two-pass gantry leveling. First pass with 10mm clearance and loose tolerance to roughly level, second pass at 2mm for precision. |

### Lighting

All status macros activate matching effects on the StealthBurner logo, StealthBurner nozzle, and Disco on a Stick simultaneously via the internal `_SET_STATUS` helper. Adding a new state only requires defining the three matching effects in the LED config files and one new macro line.

| Macro | File | Description |
|---|---|---|
| `STATUS_OFF` | `macros/lighting_control.cfg` | Turns off all LED effects. |
| `STATUS_READY` | `macros/lighting_control.cfg` | Sets LEDs to idle/ready state (RGB gradient + twinkle). |
| `STATUS_PRINTING` | `macros/lighting_control.cfg` | Sets LEDs to printing state (warm white). |
| `STATUS_HEATING_NOZZLE` | `macros/lighting_control.cfg` | Sets LEDs to nozzle heating state (orange). |
| `STATUS_HEATING_BED` | `macros/lighting_control.cfg` | Sets LEDs to bed heating state (red). |
| `STATUS_HEATSOAKING` | `macros/lighting_control.cfg` | Sets LEDs to heatsoaking state (amber). |
| `STATUS_HOMING` | `macros/lighting_control.cfg` | Sets LEDs to homing state (green). |
| `STATUS_CLEANING` | `macros/lighting_control.cfg` | Sets LEDs to cleaning state (blue). |
| `STATUS_LEVELING` | `macros/lighting_control.cfg` | Sets LEDs to gantry leveling state (purple). |
| `STATUS_MESHING` | `macros/lighting_control.cfg` | Sets LEDs to bed mesh state (lime). |
| `CASE_LIGHT_ON` | `components/case_lights.cfg` | Turns on the case lighting at full brightness. |
| `CASE_LIGHT_OFF` | `components/case_lights.cfg` | Turns off the case lighting. |
| `CASE_LIGHT_TOGGLE` | `components/case_lights.cfg` | Toggles the case lighting on/off. |

### Pause & Resume

| Macro | File | Description |
|---|---|---|
| `PAUSE` | `macros/pause_resume.cfg` | Pauses the print, parks the toolhead safely, and saves the current position for resuming. |
| `RESUME` | `macros/pause_resume.cfg` | Restores the saved position and resumes the print. |
| `CANCEL_PRINT` | `macros/pause_resume.cfg` | Cancels the print. Runs the full `PRINT_END` sequence if the printer is homed, otherwise just turns off heaters and motors. |
| `M600` | `macros/pause_resume.cfg` | Filament change command. Pauses the print and prompts the user to change filament. Resume via Mainsail or `RESUME`. |

### Filament

| Macro | File | Description |
|---|---|---|
| `LOAD_FILAMENT` | `macros/filament.cfg` | Heats the nozzle and extrudes filament to prime the nozzle. Optional parameter: `TEMP=` (default: `purge_temp_min + 10°C`). |
| `UNLOAD_FILAMENT` | `macros/filament.cfg` | Heats the nozzle, does a small purge, then retracts filament. Optional parameter: `TEMP=` (default: `purge_temp_min + 10°C`). |

### Utilities

| Macro | File | Description |
|---|---|---|
| `TEST_SPEED_FAST` | `macros/speed_test.cfg` | Runs a diagonal and edge travel speed test across the full bed at the configured travel speed. Useful for verifying max speed stability. |
| `SET_PRINT_STATS_INFO` | `macros/misc.cfg` | Override of the Klipper built-in. Updates the display with the current layer number during printing. Add `SET_PRINT_STATS_INFO TOTAL_LAYER={total_layer_count}` to slicer start gcode and `SET_PRINT_STATS_INFO CURRENT_LAYER={current_layer}` to layer change gcode. |
| `DUMP_VARIABLES` | `macros/misc.cfg` | Prints all current Klipper printer variables to the console. Optional parameter: `NAME=` to filter by keyword. Useful for debugging macros. |
| `M109` | `macros/misc.cfg` | Override of the standard M109 (wait for nozzle temp). Uses `TEMPERATURE_WAIT` with a ±1°C window instead of blocking indefinitely, allowing Klipper to process other commands. |
| `M190` | `macros/misc.cfg` | Override of the standard M190 (wait for bed temp). Same approach as M109 override. |

### Configuration

| Macro | File | Description |
|---|---|---|
| `_VARIABLES` | `macros/_variables.cfg` | Central configuration store for all positions, speeds, temperatures, and feature flags. All macros read from here — edit this file to tune printer behaviour without touching individual macros. |

Hinweis für den Slicer** — damit Layer-Anzeige in Mainsail funktioniert, in OrcaSlicer eintragen:
- Start G-Code: `SET_PRINT_STATS_INFO TOTAL_LAYER=[total_layer_count]`
- Layer Change G-Code: `SET_PRINT_STATS_INFO CURRENT_LAYER=[cur_layer]