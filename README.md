# Peltier Water Cooled Sleeping Mat

I bought a water cooled sleeping mat but the station it came with was
evaporation based, extremely loud, and needed constant refilling. It also
barely worked in hot and humid weather so I decided to build a proper
replacement using a peltier module and a real water cooling setup.

It worked but the peltier could not keep up with the heat load so I scrapped
it. Everything is documented here anyway because the build itself came out
well and the failure is worth understanding.

## What I Built

The peltier sits between a copper water block on the cold side and a
heatsink with a fan on the hot side. A brushless pump circulates coolant
through the mat. An ESP32 hosts a web UI over WiFi where you can control
fan speed, peltier power, and set presets.

The 12V PSU powers the whole system. A buck converter steps 12V down to 5V
for the ESP32.

The water block enclosure was made by cutting a section of AC cable casing
into a cube shaped sleeve to insulate the block and prevent condensation on
the outside.

Coolant is an 85/15 mix of distilled water and automotive glycol to prevent
mineral deposits, corrosion and biological growth.

## Parts

| Part | Details |
|---|---|
| Peltier module | TEC 12706, 40x40mm, 12V |
| Water block | ARSYLID pure copper, 40x40x10mm |
| Pump | 280L/H brushless DC, 12V 5W, submersible |
| Power supply | 100W 12V switching PSU |
| Motor driver | BTS7960 43A H bridge |
| Buck converter | LM2596 or MP1584, 12V to 5V |
| Microcontroller | ESP32 |
| Coolant | 85% distilled water, 15% car coolant |
| Enclosure | 3D printed |

## Wiring

![Wiring diagram](docs/images/wiring.png)

12V PSU positive to BTS7960 power input and to buck converter input.
PSU negative to shared ground.
Buck converter output 5V to ESP32 VIN, ground to shared ground.
BTS7960 RPWM and LPWM to ESP32 PWM pins.
Fan PWM wire to a separate ESP32 PWM pin.
Water block on peltier cold side, heatsink and fan on hot side.

## Images

![All parts laid out](docs/images/parts.jpg)
![Peltier and water block assembly](docs/images/peltier_waterblock.jpg)
![Condensation insulation sleeve](docs/images/sleeve.jpg)
![3D printed enclosure](docs/images/enclosure.jpg)
![Electronics and wiring](docs/images/wiring_photo.jpg)
![Full assembly](docs/images/assembly.jpg)

## Why It Failed

The TEC 12706 has a decent temperature differential on paper but in
practice it has to cool the water loop and reject that heat into the air at
the same time. When ambient temperature is high the hot side heatsink gets
warm fast, which compresses the usable temperature differential until the
peltier is basically doing nothing useful. A single module of this class
cannot cool a body contact mat in hot conditions. You would need stacked
modules, a much larger cold reservoir, or a compressor based chiller.

## What Worked Fine

The pump was extremely quiet, no issues at all. The BTS7960 ran cool and
gave smooth PWM control. The coolant loop had no leaks. The web UI worked well and the presets were
convenient.

## Lessons Learned

A peltier module moves heat, it does not generate cold. The usable
temperature differential shrinks as the hot side heats up, and in a hot
environment the hot side heatsink cannot dump heat fast enough to keep up.

The fix would be stacking multiple peltier modules, using a much larger
heatsink and fan on the hot side, or switching to a compressor based chiller
entirely. A single TEC 12706 is not enough for a body contact cooling load.
