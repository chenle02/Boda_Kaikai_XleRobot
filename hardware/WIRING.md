# XLeRobot 2-Wheel Wiring Guide

## System Overview

```
                        ┌──────────────────┐
                        │  Raspberry Pi 5  │
                        │   (Rasp-Boda)    │
                        └──┬───┬───┬───┬───┘
                           │   │   │   │
              USB-C ───────┘   │   │   └─────── USB-C
              (Serial)         │   │             (Serial)
                               │   │
                          USB  │   │  USB
                        (Cam)  │   │  (Cam)
                               │   │
    ┌──────────────────────────┘   └───────────────────────────┐
    │                                                          │
    ▼                                                          ▼
┌─────────┐  ┌──────────┐                  ┌──────────┐  ┌─────────┐
│  HEAD   │  │LEFT WRIST│                  │RIGHT     │  │  WHEEL  │
│ CAMERA  │  │ CAMERA   │                  │WRIST CAM │  │  BASE   │
│(video0) │  │(missing  │                  │ (video2) │  │         │
│         │  │ cable)   │                  │          │  │         │
└─────────┘  └──────────┘                  └──────────┘  └─────────┘
```

## USB Port Map (Raspberry Pi 5)

```
┌────────────────────────────────────────────────────────────────┐
│                      RASPBERRY PI 5                            │
│                                                                │
│   USB-A Ports (top row)          USB-A Ports (bottom row)      │
│   ┌──────┐  ┌──────┐            ┌──────┐  ┌──────┐            │
│   │ USB  │  │ USB  │            │ USB  │  │ USB  │            │
│   │ 3.0  │  │ 3.0  │            │ 2.0  │  │ 2.0  │            │
│   └──────┘  └──────┘            └──────┘  └──────┘            │
│                                                                │
│   Power (USB-C)    HDMI x2    Ethernet    microSD              │
└────────────────────────────────────────────────────────────────┘

Connected devices:
  • USB Serial (Bus A) → Feetech servo controller → Left Arm + Head
  • USB Serial (Bus B) → Feetech servo controller → Right Arm + Wheels
  • USB Camera 1 → Head camera (/dev/video0)
  • USB Camera 2 → Right wrist camera (/dev/video2)
  • USB Hub (Genesys Logic) → Keyboard + Mouse
```

## Bus A — Left Arm + Head (8 motors)

**USB Serial**: `5B14033219` → `/dev/serial/by-id/usb-1a86_USB_Single_Serial_5B14033219-if00`

Motors are daisy-chained: each motor has 2 ports — one IN, one OUT to the next motor.

```
USB Serial ──► [Motor ID 1] ──► [Motor ID 2] ──► [Motor ID 3] ──►
  Adapter       Shoulder Pan     Shoulder Lift    Elbow Flex

──► [Motor ID 4] ──► [Motor ID 5] ──► [Motor ID 6] ──►
     Wrist Flex       Wrist Roll       Gripper

──► [Motor ID 7] ──► [Motor ID 8]
     Head Pan          Head Tilt
```

### Left Arm Motor Chain Detail

```
    ┌─────────────────────────────────────────────┐
    │           LEFT ARM (SO101)                   │
    │                                              │
    │   ┌─────┐    cable    ┌─────┐    cable      │
    │   │ ID1 │────────────►│ ID2 │──────────►    │
    │   │Shldr│             │Shldr│               │
    │   │ Pan │             │Lift │               │
    │   └──▲──┘             └─────┘               │
    │      │                                       │
    │  from USB                                    │
    │  Serial                ┌─────┐    cable      │
    │  Adapter    ◄──────────│ ID3 │◄─────────    │
    │                        │Elbow│               │
    │                        │Flex │               │
    │                        └─────┘               │
    │                                              │
    │   ┌─────┐    cable    ┌─────┐    cable      │
    │   │ ID4 │────────────►│ ID5 │──────────►    │
    │   │Wrst │             │Wrst │               │
    │   │Flex │             │Roll │               │
    │   └─────┘             └─────┘               │
    │                                              │
    │                        ┌─────┐               │
    │           ◄────────────│ ID6 │               │
    │                        │Grip │               │
    │                        │ per │               │
    │                        └──┬──┘               │
    └───────────────────────────┼──────────────────┘
                                │
                           cable continues
                           to HEAD motors
                                │
                                ▼
    ┌───────────────────────────────────────────────┐
    │              HEAD                              │
    │                                                │
    │   ┌─────┐    cable    ┌─────┐                  │
    │   │ ID7 │────────────►│ ID8 │   (end of chain) │
    │   │Head │             │Head │                  │
    │   │ Pan │             │Tilt │                  │
    │   └──▲──┘             └─────┘                  │
    │      │                                         │
    │  from ID6                                      │
    │  (gripper)                                     │
    └────────────────────────────────────────────────┘
```

### Motor ID Table — Bus A

| Position in Chain | Motor Name | Motor ID | Type | Mode |
|:-:|---|:-:|---|---|
| 1 | `left_arm_shoulder_pan` | **1** | STS3215 | Position |
| 2 | `left_arm_shoulder_lift` | **2** | STS3215 | Position |
| 3 | `left_arm_elbow_flex` | **3** | STS3215 | Position |
| 4 | `left_arm_wrist_flex` | **4** | STS3215 | Position |
| 5 | `left_arm_wrist_roll` | **5** | STS3215 | Position |
| 6 | `left_arm_gripper` | **6** | STS3215 | Position |
| 7 | `head_motor_1` (pan) | **7** | STS3215 | Position |
| 8 | `head_motor_2` (tilt) | **8** | STS3215 | Position |

---

## Bus B — Right Arm + Wheels (8 motors)

**USB Serial**: `5B14112843` → `/dev/serial/by-id/usb-1a86_USB_Single_Serial_5B14112843-if00`

```
USB Serial ──► [Motor ID 1] ──► [Motor ID 2] ──► [Motor ID 3] ──►
  Adapter       Shoulder Pan     Shoulder Lift    Elbow Flex

──► [Motor ID 4] ──► [Motor ID 5] ──► [Motor ID 6] ──►
     Wrist Flex       Wrist Roll       Gripper

──► [Motor ID 9] ──► [Motor ID 10]
     Left Wheel        Right Wheel
```

### Right Arm + Wheel Chain Detail

```
    ┌─────────────────────────────────────────────┐
    │           RIGHT ARM (SO101)                  │
    │                                              │
    │   ┌─────┐    cable    ┌─────┐    cable      │
    │   │ ID1 │────────────►│ ID2 │──────────►    │
    │   │Shldr│             │Shldr│               │
    │   │ Pan │             │Lift │               │
    │   └──▲──┘             └─────┘               │
    │      │                                       │
    │  from USB                                    │
    │  Serial                ┌─────┐    cable      │
    │  Adapter    ◄──────────│ ID3 │◄─────────    │
    │                        │Elbow│               │
    │                        │Flex │               │
    │                        └─────┘               │
    │                                              │
    │   ┌─────┐    cable    ┌─────┐    cable      │
    │   │ ID4 │────────────►│ ID5 │──────────►    │
    │   │Wrst │             │Wrst │               │
    │   │Flex │             │Roll │               │
    │   └─────┘             └─────┘               │
    │                                              │
    │                        ┌─────┐               │
    │           ◄────────────│ ID6 │               │
    │                        │Grip │               │
    │                        │ per │               │
    │                        └──┬──┘               │
    └───────────────────────────┼──────────────────┘
                                │
                           cable continues
                           to WHEEL motors
                                │
                                ▼
    ┌────────────────────────────────────────────────┐
    │          DIFFERENTIAL WHEEL BASE               │
    │                                                │
    │   ┌──────┐    cable    ┌──────┐                │
    │   │ ID 9 │────────────►│ ID10 │  (end of chain)│
    │   │ Left │             │Right │                │
    │   │Wheel │             │Wheel │                │
    │   └──▲───┘             └──────┘                │
    │      │                                         │
    │  from ID6                                      │
    │  (gripper)                                     │
    └────────────────────────────────────────────────┘
```

### Motor ID Table — Bus B

| Position in Chain | Motor Name | Motor ID | Type | Mode |
|:-:|---|:-:|---|---|
| 1 | `right_arm_shoulder_pan` | **1** | STS3215 | Position |
| 2 | `right_arm_shoulder_lift` | **2** | STS3215 | Position |
| 3 | `right_arm_elbow_flex` | **3** | STS3215 | Position |
| 4 | `right_arm_wrist_flex` | **4** | STS3215 | Position |
| 5 | `right_arm_wrist_roll` | **5** | STS3215 | Position |
| 6 | `right_arm_gripper` | **6** | STS3215 | Position |
| 7 | `base_left_wheel` | **9** | STS3215 | Velocity |
| 8 | `base_right_wheel` | **10** | STS3215 | Velocity |

> **Note**: Wheel IDs are **9 and 10** (not 7 and 8). This avoids conflict with head motor IDs.
> Wheels use **Velocity mode**, all other motors use **Position mode**.

---

## Camera Connections

```
┌─────────────┐     USB      ┌───────────────────┐
│  Head Cam   │─────────────►│ Pi USB port        │
│ (ARC Intl)  │              │ xhci-hcd.1-1       │
│             │              │ → /dev/video0       │
└─────────────┘              └───────────────────  ┘

┌─────────────┐     USB      ┌───────────────────┐
│ Right Wrist │─────────────►│ Pi USB port        │
│   Camera    │              │ xhci-hcd.0-2.2     │
│ (ARC Intl)  │              │ → /dev/video2       │
└─────────────┘              └───────────────────  ┘

┌─────────────┐
│ Left Wrist  │  ❌ Not connected (missing cable)
│   Camera    │
└─────────────┘
```

---

## Power Wiring

```
┌───────────────────┐
│  Anker SOLIX C300 │
│  Battery Pack     │
│                   │
│  DC Out ──────────┼──► Raspberry Pi 5 (USB-C PD)
│                   │
│  DC Out ──────────┼──► Feetech Servo Power
│                   │     (powers ALL motors on both buses)
│                   │
└───────────────────┘
```

> **Important**: The servo motors get power through the bus cable daisy chain.
> If a motor in the chain is disconnected, all downstream motors lose power AND communication.

---

## Motor ID Configuration

Each STS3215 servo ships with **factory default ID = 1**. Before daisy-chaining,
each motor must be assigned a unique ID by connecting it **one at a time** to the bus.

### Tools for Setting Motor IDs

1. **Web tool (easiest)**: [Bambot Feetech Config](https://bambot.org/feetech.js)
   - Connect ONE motor to the USB serial adapter
   - Open the web tool in Chrome
   - Select the serial port
   - Change the motor ID

2. **LeRobot setup_motors()** (Python):
   ```bash
   source ~/lerobot-env/bin/activate
   cd ~/lerobot
   python3 -c "
   from lerobot.robots.xlerobot_2wheels.xlerobot_2wheels import XLerobot2Wheels
   from lerobot.robots.xlerobot_2wheels.config_xlerobot_2wheels import XLerobot2WheelsConfig
   config = XLerobot2WheelsConfig()
   robot = XLerobot2Wheels(config)
   robot.setup_motors()
   "
   ```
   This will interactively prompt you to connect each motor one at a time.

### ID Assignment Checklist

Connect motors **one at a time** to the serial adapter and set their IDs:

**Bus A (left arm + head)**:
- [ ] Motor → ID 1 (left shoulder pan)
- [ ] Motor → ID 2 (left shoulder lift)
- [ ] Motor → ID 3 (left elbow flex)
- [ ] Motor → ID 4 (left wrist flex)
- [ ] Motor → ID 5 (left wrist roll)
- [ ] Motor → ID 6 (left gripper)
- [ ] Motor → ID 7 (head pan)
- [ ] Motor → ID 8 (head tilt)

**Bus B (right arm + wheels)**:
- [ ] Motor → ID 1 (right shoulder pan)
- [ ] Motor → ID 2 (right shoulder lift)
- [ ] Motor → ID 3 (right elbow flex)
- [ ] Motor → ID 4 (right wrist flex)
- [ ] Motor → ID 5 (right wrist roll)
- [ ] Motor → ID 6 (right gripper)
- [ ] Motor → ID 9 (left wheel)
- [ ] Motor → ID 10 (right wheel)

---

## Diagnostic Scan Results (2026-03-21)

| Bus | Expected Motors | Found | Missing |
|-----|----------------|-------|---------|
| A (5B14033219) | IDs 1-6, 7, 8 | **1, 2, 3, 4, 5, 6** | **7, 8** (head) |
| B (5B14112843) | IDs 1-6, 9, 10 | **2, 3, 4, 5, 6** | **1** (shoulder pan), **9, 10** (wheels) |

### Likely Causes

1. **Head motors (IDs 7, 8)**: Not daisy-chained from left arm gripper (ID 6), OR IDs not set yet
2. **Wheel motors (IDs 9, 10)**: Not daisy-chained from right arm gripper (ID 6), OR IDs not set yet (factory default is ID 1, needs to be changed to 9 and 10)
3. **Right arm ID 1**: Loose cable at the first motor in the chain, or ID conflict if a wheel motor still has factory ID 1

### Troubleshooting Steps

1. **Check physical cable from left gripper (ID 6) → head pan (ID 7)**
   - The OUT port of ID 6 must connect to the IN port of ID 7
2. **Check physical cable from right gripper (ID 6) → left wheel (ID 9)**
   - The OUT port of ID 6 must connect to the IN port of ID 9
3. **Check the first cable on Bus B** (USB adapter → right shoulder pan ID 1)
4. **If wheels have never been ID'd**: They still have factory ID 1, which conflicts with the arm's ID 1. Connect each wheel motor alone and set ID to 9 / 10.

---

## Quick Verification Script

After fixing wiring, run this from the Pi:

```bash
source ~/lerobot-env/bin/activate
python3 << 'EOF'
from lerobot.motors.feetech import FeetechMotorsBus
from lerobot.motors.motors_bus import Motor, MotorNormMode

M = MotorNormMode.RANGE_M100_100
G = MotorNormMode.RANGE_0_100

bus_a = {
    'left_shoulder_pan': Motor(1, 'sts3215', M),
    'left_shoulder_lift': Motor(2, 'sts3215', M),
    'left_elbow_flex': Motor(3, 'sts3215', M),
    'left_wrist_flex': Motor(4, 'sts3215', M),
    'left_wrist_roll': Motor(5, 'sts3215', M),
    'left_gripper': Motor(6, 'sts3215', G),
    'head_pan': Motor(7, 'sts3215', M),
    'head_tilt': Motor(8, 'sts3215', M),
}

bus_b = {
    'right_shoulder_pan': Motor(1, 'sts3215', M),
    'right_shoulder_lift': Motor(2, 'sts3215', M),
    'right_elbow_flex': Motor(3, 'sts3215', M),
    'right_wrist_flex': Motor(4, 'sts3215', M),
    'right_wrist_roll': Motor(5, 'sts3215', M),
    'right_gripper': Motor(6, 'sts3215', G),
    'base_left_wheel': Motor(9, 'sts3215', M),
    'base_right_wheel': Motor(10, 'sts3215', M),
}

for label, port, motors in [
    ('Bus A (left+head)', '/dev/serial/by-id/usb-1a86_USB_Single_Serial_5B14033219-if00', bus_a),
    ('Bus B (right+wheels)', '/dev/serial/by-id/usb-1a86_USB_Single_Serial_5B14112843-if00', bus_b),
]:
    try:
        bus = FeetechMotorsBus(port=port, motors=motors)
        bus.connect()
        pos = bus.sync_read('Present_Position')
        print(f'\n{label}: ALL {len(pos)} MOTORS OK')
        for name, p in pos.items():
            print(f'  {name} (ID {motors[name].id}): pos={p}')
        bus.disconnect()
    except Exception as e:
        print(f'\n{label}: {e}')
EOF
```

Expected output when everything is connected:
```
Bus A (left+head): ALL 8 MOTORS OK
Bus B (right+wheels): ALL 8 MOTORS OK
```
