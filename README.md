# Peltier Water-Cooled Sleeping Mat

A DIY closed-loop hydronic cooling system built to replace the noisy,
evaporation-based station that came with a commercial water-cooled sleeping
mat — designed for hot and humid conditions (Ashkelon, Israel, summers).

**Status: Documented failure.** The system worked electrically and
mechanically, but the TEC1-12706 peltier module lacked sufficient cooling
capacity for the thermal load. Parts salvaged. Lessons documented.

---

## The problem

Commercial water-cooled mats often use evaporative cooling, which:
- Is loud
- Requires frequent refilling
- Performs poorly in high humidity (like coastal southern Israel)

The goal was a fully closed, quiet, thermoelectric replacement.

## System overview

| Component | Part | Notes |
|---|---|---|
| Peltier module | TEC1-12706 (40×40mm, 12V) | Cooling element |
| Water block | ARSYLID 40×40×10mm copper | Cold side heat exchanger |
| Pump | 280L/H brushless DC, 12V 5W | Ultra-quiet submersible |
| Power supply | 100W 12V switching PSU | AC 176–264V input |
| Motor driver | BTS7960 43A H-bridge | Peltier + fan PWM control |
| Temp sensor | DS18B20 waterproof, 1M cable | Closed-loop feedback |
| Coolant | 85% distilled water / 15% car coolant | Inhibits corrosion and algae |
| Enclosure | 3D printed custom case | Anti-condensation insert from AC cable casing |

## How it works

The peltier module sits sandwiched between a water block (cold side) and a
heatsink + fan (hot side). A brushless pump circulates coolant through the
mat. An ESP32 runs a web server exposing a control panel for fan speed,
peltier power, and presets.

The water block enclosure was made by cutting a section of AC power cable
casing into a cube-shaped sleeve — a simple way to insulate the block and
prevent condensation on the exterior.

Coolant is an 85/15 mix of distilled (demineralised) water and automotive
glycol coolant, chosen to prevent mineral deposits, corrosion, and
biological growth in the closed loop.

## Web controller

The ESP32 hosts a local web UI accessible over Wi-Fi with:
- Fan speed slider (PWM)
- Peltier power slider (via BTS7960)
- Temperature readout (DS18B20)
- Named presets (Sleep, Max Cool, Off)

## Why it failed

The TEC1-12706 has a max ΔT of ~66°C in ideal conditions, but it
simultaneously has to:

1. Cool the water in the loop
2. Reject heat on the hot side into ambient air

In a hot, humid environment (ambient >30°C), the hot side heatsink
temperature rises quickly. This compresses the usable ΔT, and the peltier
ends up pumping heat *into* the mat rather than out of it under load.

A single 12706-class peltier cannot meaningfully cool a body-contact mat
in these conditions. You'd need either stacked modules, compressor-based
refrigeration (like a real chiller), or a much larger cold reservoir.

See [`docs/thermal_analysis.md`](docs/thermal_analysis.md) for a full breakdown.

## What worked

- Pump: extremely quiet, zero issues
- BTS7960 driver: smooth PWM control, ran cool
- DS18B20: accurate, waterproof, easy to integrate
- Coolant loop: no leaks, no deposits
- 3D printed enclosure: solid
- Web UI: worked well, presets were convenient

## Lessons learned

See [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)

## License

MIT
