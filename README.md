# Automated 3D Optical Scanner (MeshMapper)

An automated photogrammetry turntable and optical acquisition rig built on a Raspberry Pi 4B host. The platform synchronizes physical angular stepping of a high-torque mechanical turntable with an Arducam IMX519 autofocus camera module over MIPI CSI-2, generating structured 360-degree image datasets for photogrammetric mesh generation.

---

## Engineering Scope & Hardware Contributions

* **Dedicated Custom Interface & Power PCB:** Designed and routed a custom 2-layer carrier board in KiCad from reference schematics to eliminate loose breadboard wiring. Resolved electrical noise and inductive kickback by isolating high-current 12V motor driver traces from the clean 5V logic power delivery feeding the Raspberry Pi and camera sensor.
* **Electromechanical Stage Integration:** Assembled and calibrated a precision stepper-driven turntable indexing mechanism to achieve consistent angular steps without angular overshoot or drift.
* **Firmware & Capture Synchronization:** Adapted and modified Python automation routines to sequence turntable motor stepping, settle physical vibrations, and trigger exposure capture on the Arducam IMX519 via `libcamera` without motion blur.
* **3D Point-Cloud Pipeline:** Integrated open-source photogrammetry reconstruction workflows to process exported 360-degree image batches into point-clouds and 3D surface meshes (.OBJ/.STL).

---

## System Architecture

```text
┌────────────────────────────────────────────────────────────────────────┐
│                        Power Distribution (Custom PCB)                 │
│                                                                        │
│   [ External 12V Supply ] ──┬──> [ Stepper Motor Driver Rail (12V) ]   │
│                             │                                          │
│                             └──> [ Regulated Logic Rail (5V) ]         │
└──────────────────────────────────────────────┬─────────────────────────┘
                                               │
                                               ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        Host Controller (Raspberry Pi 4B)               │
│                                                                        │
│   [ Python Orchestration Script ]                                      │
│        │                                                               │
│        ├── GPIO Stepping Control ──> Stepper Driver (A4988 / TMC2208)  │
│        │                             - Turntable Indexing              │
│        │                                                               │
│        └── MIPI CSI-2 Pipeline ────> Arducam IMX519 (16MP Autofocus)   │
│            (libcamera / V4L2)        - Vibration-Settled Capture       │
└──────────────────────────────────────┬─────────────────────────────────┘
                                       │
                                       ▼ Output Artifacts
┌────────────────────────────────────────────────────────────────────────┐
│                    Structured 360° Image Batches                       │
│             Downstream Photogrammetry Engine (.OBJ / .STL)             │
└────────────────────────────────────────────────────────────────────────┘
