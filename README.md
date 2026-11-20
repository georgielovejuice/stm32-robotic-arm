# Multi-Mode Robotic Arm

Voice-controlled 6-DOF arm. Say **"pick, red box, yes"**: a depth camera finds the block, the STM32 solves IK, and the arm places it on your hand.

![STM32](https://img.shields.io/badge/STM32-F767ZI-03234B?logo=stmicroelectronics&logoColor=white)
![C](https://img.shields.io/badge/C-bare--metal-A8B9CC?logo=c&logoColor=black)
![Python](https://img.shields.io/badge/Python-RealSense-3776AB?logo=python&logoColor=white)
![Raspberry Pi](https://img.shields.io/badge/Raspberry%20Pi-4-A22846?logo=raspberrypi&logoColor=white)
![Fusion](https://img.shields.io/badge/CAD-Fusion-FF6B00?logo=autodesk&logoColor=white)

<p align="center">
  <img src="docs/media/hero-arm.jpg" alt="Robotic arm next to the drop station with coloured blocks" width="820">
</p>

<p align="center">
  <a href="https://youtu.be/xyfyx_k8d-4"><b>▶ Demo video</b></a> ·
  <a href="https://github.com/georgielovejuice/stm32-robotic-arm/releases/latest/download/final-report.pdf"><b>📄 Report</b></a>
</p>

## Features

- **On-chip keyword spotting**: CNN (X-CUBE-AI) on 2× INMP441 I²S mics, 21 words, ~43 ms per inference
- **Closed-form IK + FK**: four modes: Voice, Cartesian, Joint Group, Single-Joint
- **Depth vision**: RealSense D435 on a Pi 4, RANSAC table plane + height/colour segmentation
- **Camera → robot calibration**: Kabsch/SVD, 8.9 mm RMS
- **50 Hz motion pipeline**: trapezoidal ramps, synchronised joints, joint limits
- **Bare-metal firmware**: ISR + main loop, event queue, DMA for audio, ADC and UART

## Architecture

![System block diagram](docs/media/block-diagram.png)

```text
STM32 → Pi   FIND red_box
Pi → STM32   OBJ red_box 240 12 30     # mm, robot frame, 6–11 ms reply
```

## Vision

![Vision pipeline](docs/media/vision-pipeline.jpg)
![Hand tracking](docs/media/vision-hand.jpg)

## Hardware

| | |
|---|---|
| ![System top view](docs/media/system-top.jpg) | ![LCD pages](docs/media/lcd-pages.jpg) |
| ![Arm CAD](docs/media/cad-arm.png) | ![Console CAD](docs/media/cad-console.png) |

NUCLEO-F767ZI · Pi 4 + D435 · 3× MG996R + 3× SG92R · ILI9341 touch LCD · KY-023 + KY-040 · 3S LiPo → 6.0 V / 5.1 V bucks

<details>
<summary>Flowchart, voice state machine, wiring</summary>

![Flowchart](docs/media/flowchart.png)
![Voice states](docs/media/voice-states.png)
![Connectivity](docs/media/connectivity.png)
![Circuit](docs/media/circuit.png)

</details>

## Results

| Metric | Result |
|---|---|
| Voice command accuracy | 75–99% confidence, 0 false accepts |
| Positioning (calibration RMS) | 8.9 mm |
| Grasp success | ~75–80% |
| Consecutive pick-and-place | 7–8 / 10 (all misses came from camera calibration) |

## Build

```sh
python ProjectDevGuide/ci/build_firmware.py   # STM32CubeIDE 2.2 toolchain, 0 warnings
sh ProjectDevGuide/tests/host/run.sh          # host tests
```

Firmware in `RoboticArmMain/App/`, Pi code in `ProjectDevGuide/tools/pi/`.

---

KMITL Microcontroller Interfacing 2026, team of 7. Forked from [Potaeeatsthis/2026-micro-con](https://github.com/Potaeeatsthis/2026-micro-con). Arm based on [OmArTronics 6-DOF](https://omartronics.com/diy-6-dof-robotic-arm-build/).
