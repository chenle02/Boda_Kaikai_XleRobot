# XLeRobot Software Setup — Raspberry Pi 5

## Pi Details

| Field | Value |
|-------|-------|
| Hostname | `Rasp-Boda` |
| SSH alias | `ssh Bokai` (configured in `~/.ssh/config`) |
| IP | `192.168.86.20` |
| User | `bokai` |
| OS | Manjaro ARM (Arch-based) |
| Kernel | 6.12.41 aarch64 (RPi5) |
| RAM | 16 GB |
| Disk | 59 GB (46 GB free) |
| Python | 3.13.7 |

## Installation (completed 2026-03-21)

### 1. System Dependencies

Most packages pre-installed on Manjaro ARM. Key packages:
- `python 3.13.7`, `git`, `curl`, `wget`, `base-devel`, `v4l-utils`
- `pip` available via `python3 -m ensurepip`

### 2. Virtual Environment & LeRobot

```bash
python3 -m venv ~/lerobot-env
source ~/lerobot-env/bin/activate
cd ~/lerobot
pip install -e '.[feetech]'
```

- LeRobot v0.5.1 installed in editable mode at `~/lerobot/`
- Feetech servo SDK included
- PyTorch 2.10.0 (aarch64)

### 3. XLeRobot Integration

XLeRobot source cloned to `~/XLeRobot/`. Files copied into LeRobot:

```
~/lerobot/src/lerobot/robots/xlerobot/       ← robot definition
~/lerobot/src/lerobot/robots/xlerobot_2wheels/ ← 2-wheel variant
~/lerobot/src/lerobot/model/SO101Robot.py     ← analytical IK solver
~/lerobot/src/lerobot/teleporators/           ← VR teleop modules
~/lerobot/examples/xlerobot/                  ← example scripts
```

### 4. Serial Port Configuration

User `bokai` added to `uucp` group for serial access.

Config updated to use persistent `/dev/serial/by-id/` paths:

| Bus | Serial ID | Persistent Path | Role |
|-----|-----------|-----------------|------|
| A | `5B14033219` | `/dev/serial/by-id/usb-1a86_USB_Single_Serial_5B14033219-if00` | Left arm + Head |
| B | `5B14112843` | `/dev/serial/by-id/usb-1a86_USB_Single_Serial_5B14112843-if00` | Right arm + Wheels |

### 5. Camera Configuration

| Camera | Device | Status |
|--------|--------|--------|
| Head | `/dev/video0` (usb-xhci-hcd.1-1) | ✅ Working 640x480 |
| Right wrist | `/dev/video2` (usb-xhci-hcd.0-2.2) | ✅ Working 640x480 |
| Left wrist | — | ❌ Missing cable |

## Hardware Status

### Motors Detected (2026-03-21)

**Bus A** (left arm + head): 6 of 8 motors found
- ✅ Left arm IDs 1-6
- ❌ Head motors IDs 7, 8 — not detected

**Bus B** (right arm + wheels): 5 of 9 motors found
- ✅ Right arm IDs 2-6
- ❌ Right arm ID 1 — not detected (loose cable?)
- ❌ Wheel motors IDs 7, 8, 9 — not detected

### Action Items

1. **Check head motor wiring** — IDs 7, 8 should be daisy-chained on Bus A
2. **Check wheel motor wiring** — IDs 9, 10 should be daisy-chained on Bus B (2-wheel variant)
3. **Check right arm motor ID 1 cable** — may be loose, or ID conflict with un-configured wheel motor
4. **Verify motor IDs** using [Bambot config tool](https://bambot.org/feetech.js):
   - Head motors: IDs 7, 8
   - Wheel motors: IDs 9 (left), 10 (right) — **NOT 7,8 like 3-wheel version**
5. **Get left wrist camera cable**

## Quick Reference

```bash
# SSH into the Pi
ssh Bokai

# Activate environment
source ~/lerobot-env/bin/activate

# Test motor connectivity
cd ~/lerobot
python3 -c "
from lerobot.robots.xlerobot.config_xlerobot import XLerobotConfig
c = XLerobotConfig()
print(f'port1: {c.port1}')
print(f'port2: {c.port2}')
print(f'cameras: {list(c.cameras.keys())}')
"

# Keyboard teleop (once all motors calibrated)
PYTHONPATH=. python examples/xlerobot/4_xlerobot_teleop_keyboard.py

# Camera test
python3 -c "
import cv2
for dev in ['/dev/video0', '/dev/video2']:
    cap = cv2.VideoCapture(dev)
    ret, frame = cap.read()
    print(f'{dev}: {frame.shape if ret else \"FAIL\"!s}')
    cap.release()
"
```

## Motor ID Reference

### Bus A (port1) — Left Arm + Head

| Motor | ID | Type |
|-------|-----|------|
| left_arm_shoulder_pan | 1 | STS3215 |
| left_arm_shoulder_lift | 2 | STS3215 |
| left_arm_elbow_flex | 3 | STS3215 |
| left_arm_wrist_flex | 4 | STS3215 |
| left_arm_wrist_roll | 5 | STS3215 |
| left_arm_gripper | 6 | STS3215 |
| head_motor_1 | 7 | STS3215 |
| head_motor_2 | 8 | STS3215 |

### Bus B (port2) — Right Arm + Wheels (2-Wheel Differential Drive)

| Motor | ID | Type | Mode |
|-------|-----|------|------|
| right_arm_shoulder_pan | 1 | STS3215 | Position |
| right_arm_shoulder_lift | 2 | STS3215 | Position |
| right_arm_elbow_flex | 3 | STS3215 | Position |
| right_arm_wrist_flex | 4 | STS3215 | Position |
| right_arm_wrist_roll | 5 | STS3215 | Position |
| right_arm_gripper | 6 | STS3215 | Position |
| base_left_wheel | **9** | STS3215 | **Velocity** |
| base_right_wheel | **10** | STS3215 | **Velocity** |

> See [hardware/WIRING.md](../hardware/WIRING.md) for full wiring diagram and troubleshooting.
