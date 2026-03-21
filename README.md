# Boda & Kaikai's XLeRobot Build

[![Apache License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Our journey building the [XLeRobot](https://github.com/Vector-Wangel/XLeRobot) — a practical dual-arm mobile home robot for ~$660.

## About This Project

This repository documents **Boda and Kaikai's** build of the XLeRobot, following the open-source design by [Vector-Wangel/XLeRobot](https://github.com/Vector-Wangel/XLeRobot). We track our hardware assembly, software setup, customizations, and experiments here.

## Repository Structure

```
hardware/       - Hardware assembly notes, modifications, and 3D printing files
software/       - Software setup, configurations, and custom scripts
simulation/     - Simulation environments and testing
docs/           - Documentation, build logs, and photos
web_control/    - Web-based control interface
others/         - Miscellaneous resources
```

## Assembly Guide

Estimated build time: **2-4 hrs** from scratch, **1-2 hrs** with pre-assembled SO101 arms.

### Step 1 — Buy Parts

[Bill of Materials](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/material.html) — full parts list with purchase links for US/EU/CN/IN.

### Step 2 — 3D Print

[3D Printing Guide](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/3d.html) — print settings, filament recommendations, and layout tips.

- Download the [3D model files (.3mf / .stl)](https://github.com/Vector-Wangel/XLeRobot/tree/main/hardware) from the original repo
- [STEP CAD files](https://github.com/Vector-Wangel/XLeRobot/tree/main/hardware/step) available for custom modifications
- Recommended printer: BambuLab A1 with PLA Matte Black (~$15-25 in filament)

### Step 3 — Assemble

[Main Assembly Instructions (3-wheel)](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/assemble.html) — the primary build guide with step-by-step photos.

Assembly sequence:

| Step | Component | Guide |
|------|-----------|-------|
| 1 | SO101 Arms (build 2x follower arms) | [HuggingFace SO101 docs](https://huggingface.co/docs/lerobot/so101) |
| 2 | Configure Motors (head: IDs 7,8 / wheels: IDs 7,8,9) | [Bambot motor config tool](https://bambot.org/feetech.js) |
| 3 | IKEA RASKOG Cart | [IKEA cart manual (PDF)](https://github.com/Vector-Wangel/XLeRobot/blob/main/others/Manuals_raskog_utility_cart.pdf) |
| 4 | Wheel Base (omni-wheels, plates, connectors) | [Assembly page — Wheel Base](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/assemble.html#wheel-base) |
| 5 | Arm Base + Head | [Assembly page — Arm Base](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/assemble.html#arm-base) |
| 6 | Wiring (motor cables, USB-C, power) | [Assembly page — Wiring](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/assemble.html#wiring) |
| 7 | Battery (Anker SOLIX C300) | [Battery manual (PDF)](https://github.com/Vector-Wangel/XLeRobot/blob/main/others/Manual_Anker_SOLIX_C300_DC_Portable_Power_Station.pdf) |
| 8 | Final Assembly + Optional Visual Upgrades | [Assembly page — Final](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/assemble.html#final-assembly) |

Alternative: [Dual-wheel version assembly (v0.4.0)](https://xlerobot.readthedocs.io/en/latest/hardware/getting_started/assemble_2wheel.html) — more stable differential-wheel base at a cheaper price.

### Step 4 — Software

[Get your robot moving!](https://xlerobot.readthedocs.io/en/latest/software/index.html) — software setup, motor calibration, and control.

### Video Tutorials

- [Wowrobo official assembly video (YouTube)](https://www.youtube.com/watch?v=upB1CEFeOlk)
- [Greg's Tech assembly tips (YouTube)](https://www.youtube.com/watch?v=Ezy3EepLSm4)

### Component Documentation

| Component | Source |
|-----------|--------|
| SO101 arm assembly | [HuggingFace LeRobot docs](https://huggingface.co/docs/lerobot/so101) |
| LeKiwi base wiring | [LeKiwi Assembly.md](https://github.com/SIGRobotics-UIUC/LeKiwi/blob/main/Assembly.md) |
| Wrist camera mount | [SO-ARM100 Optional](https://github.com/TheRobotStudio/SO-ARM100/tree/main/Optional/SO101_Wrist_Cam_Hex-Nut_Mount_32x32_UVC_Module) |
| Motor configuration | [Bambot web tool](https://bambot.org/feetech.js) |
| RGBD gimbal (optional) | [Gimbal STEP files + exploded view](https://github.com/Vector-Wangel/XLeRobot/tree/main/hardware/step/RGBD_Gimbal) |

## Reference

- **Original Project**: [Vector-Wangel/XLeRobot](https://github.com/Vector-Wangel/XLeRobot)
- **Documentation**: [xlerobot.readthedocs.io](https://xlerobot.readthedocs.io/en/latest/)
- **Discord Community**: [XLeRobot Discord](https://discord.gg/bjZveEUh6F)
- **Simulation / VR**: [XLeVR](https://github.com/Vector-Wangel/XLeRobot/tree/main/XLeVR) | [MuJoCo + 3DGS Web Demo](https://vector-wangel.github.io/MuJoCo-GS-Web/)

## Built Upon

This project stands on the shoulders of giants:
- [LeRobot](https://github.com/huggingface/lerobot) by Hugging Face
- [SO-100/SO-101](https://github.com/TheRobotStudio/SO-ARM100) by The Robot Studio
- [LeKiwi](https://github.com/SIGRobotics-UIUC/LeKiwi) by SIGRobotics UIUC
- [Bambot](https://github.com/timqian/bambot)

## License

This project is licensed under the Apache License 2.0 — see the [LICENSE](LICENSE) file for details.
