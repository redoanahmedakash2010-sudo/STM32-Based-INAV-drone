# STM32-Based INAV Autonomous Drone

A custom DIY autonomous drone project built with an STM32 flight controller running INAV 6.0 firmware, featuring GPS navigation, waypoints, and telemetry setup.

---

## 🛠️ Hardware Overview

* **Flight Controller:** STM32F411 Black Pill
* **Firmware:** INAV 6.0
* **GPS & Compass:** M8N GPS Module with integrated magnetometer
* **Receiver:** ELRS / Serial RX
* **Frame & Motors:** Custom Quadcopter Setup

---

## 🚀 Features

* **Autonomous Flight:** Waypoint navigation and Return-To-Home (RTH) capabilities.
* **Failsafe Modes:** Automatic landing and RTH on signal loss or low battery.
* **Telemetry:** Real-time flight data logging and OSD setup.

---

## 📂 Repository Structure

```text
├── CLI-Dumps/          # INAV configuration backup files (.cli)
├── Schematics/         # Wiring diagrams and pinout connections
└── README.md           # Project documentation
