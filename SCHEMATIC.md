# Wiring Schematic 🔌

**Complete wiring diagrams and pin assignments for STM32F411 INAV Drone**

---

## Overview Diagram

```
                    ┌─────────────────────────────┐
                    │    STM32F411 BLACK PILL     │
                    │    Flight Controller        │
                    └─────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
         GPS Module       Motor ESCs      Radio Receiver
         (NEO-7M)      (4x 30A BLHELI)    (NRF24L01+)
              │               │               │
              └───────────────┼───────────────┘
                              │
                         4 Motors
                       (RS2205 2300KV)
                              │
                         Propellers
```

---

## STM32F411 Black Pill Pinout

```
                    STM32F411 BLACK PILL
    
    ┌─────────────────────────────────────────┐
    │  USB  (Micro-USB for programming)       │
    ├─────────────────────────────────────────┤
    │                                          │
    │  GND  ○●●○  GND        ○●●○  GND        │
    │  GND  ○●●○  GND        ○●●○  GND        │
    │  PC13 ○●●○  3.3V       ○●●○  5V         │
    │  PC14 ○●●○  PC15       ○●●○  GND        │
    │  PC0  ○●●○  PA0(M1)    ○●●○  A VREF     │
    │  PC1  ○●●○  PA1(M2)    ○●●○  PC2        │
    │  PC2  ○●●○  PA2(M3)    ○●●○  PC3        │
    │  PC3  ○●●○  PA3(M4)    ○●●○  VDDA       │
    │  VDDA ○●●○  PA4        ○●●○  VSS        │
    │  VSS  ○●●○  PA5(NRF)   ○●●○  PA8        │
    │  PB9  ○●●○  PA6(GPS)   ○●●○  PA9(GPS)   │
    │  PB8  ○●●○  PA7(NRF)   ○●●○  PA10(GPS)  │
    │  PB7  ○●●○  PB0(NRF)   ○●●○  PA11       │
    │  PB6  ○●●○  PB1(NRF)   ○●●○  PA12       │
    │  PB5  ○●●○  PB2        ○●●○  PA13       │
    │  PB4  ○●●○  PB10       ○●●○  PA14       │
    │  PB3  ○●●○  PB11       ○●●○  PA15       │
    │  PA15 ○●●○  GND        ○●●○  GND        │
    │                                          │
    └─────────────────────────────────────────┘
    
Legend:
    ○●●○  = Pin row
    (M1-4) = Motor outputs
    (GPS)  = GPS UART pins
    (NRF)  = NRF SPI pins
```

---

## Detailed Pin Assignment Table

```
╔════════════════════════════════════════════════════════════════╗
║ STM32F411 PIN ASSIGNMENT FOR INAV DRONE                        ║
╠════════════════════════════════════════════════════════════════╣
║ Function        │ Pin   │ Direction │ Note                    ║
╠════════════════════════════════════════════════════════════════╣
║ MOTOR 1 (FR)    │ PA0   │ OUT       │ PWM signal to ESC1      ║
║ MOTOR 2 (RL)    │ PA1   │ OUT       │ PWM signal to ESC2      ║
║ MOTOR 3 (FL)    │ PA2   │ OUT       │ PWM signal to ESC3      ║
║ MOTOR 4 (RR)    │ PA3   │ OUT       │ PWM signal to ESC4      ║
╠════════════════════════════════════════════════════════════════╣
║ GPS TX (UART1)  │ PA9   │ OUT       │ Serial TX to GPS RX     ║
║ GPS RX (UART1)  │ PA10  │ IN        │ Serial RX from GPS TX   ║
╠════════════════════════════════════════════════════════════════╣
║ IMU SCL (I2C1)  │ PC0   │ BIDIR     │ Clock line for GY-87    ║
║ IMU SDA (I2C1)  │ PC1   │ BIDIR     │ Data line for GY-87     ║
╠════════════════════════════════════════════════════════════════╣
║ NRF SCK (SPI1)  │ PA5   │ OUT       │ SPI clock for NRF24     ║
║ NRF MISO (SPI1) │ PA6   │ IN        │ SPI data in from NRF24  ║
║ NRF MOSI (SPI1) │ PA7   │ OUT       │ SPI data out to NRF24   ║
║ NRF CSN         │ PB0   │ OUT       │ Chip select for NRF24   ║
║ NRF CE          │ PB1   │ OUT       │ Chip enable for NRF24   ║
╠════════════════════════════════════════════════════════════════╣
║ BEEPER          │ PA8   │ OUT       │ Buzzer output           ║
║ STATUS LED      │ PC13  │ OUT       │ Built-in LED (red)      ║
╠════════════════════════════════════════════════════════════════╣
║ POWER SUPPLY    │ 5V    │ IN        │ From battery via BEC    ║
║ GROUND          │ GND   │ COM       │ Common ground           ║
║ GROUND (I2C)    │ GND   │ COM       │ Pull-down reference     ║
╚════════════════════════════════════════════════════════════════╝
```

---

## Connection Diagram - GPS Module (NEO-7M)

```
┌──────────────────────┐
│   NEO-7M GPS Module  │
├──────────────────────┤
│                      │
│  VCC ────────────────→ 3.3V or 5V (check datasheet)
│  GND ────────────────→ GND (Common ground)
│  TX  ────────────────→ PA10 (STM32 RX / UART1)
│  RX  ────────────────→ PA9  (STM32 TX / UART1)
│  GND ────────────────→ GND
│                      │
│ ┌─ Antenna socket    │
│ │ (external antenna  │
│ │  recommended)      │
│                      │
└──────────────────────┘

UART1 Configuration:
- Baud Rate: 38400
- Data Bits: 8
- Stop Bits: 1
- Parity: None
```

---

## Connection Diagram - IMU Module (GY-87)

```
┌──────────────────────┐
│   GY-87 IMU Module   │
├──────────────────────┤
│                      │
│  VCC ────────────────→ 3.3V (IMPORTANT! Not 5V)
│  GND ────────────────→ GND (Common ground)
│  SCL ────────────────→ PC0 (STM32 I2C1 SCL)
│  SDA ────────────────→ PC1 (STM32 I2C1 SDA)
│  INT ────────────────→ Not used (optional)
│  GND ────────────────→ GND
│                      │
│ Contains:            │
│  - MPU6050 (Gyro)    │
│  - MPU6050 (Accel)   │
│  - Optional compass  │
│                      │
└──────────────────────┘

I2C1 Configuration:
- Speed: 400kHz (standard)
- Pull-ups: 4.7kΩ (on SDA & SCL to 3.3V)
- Address: 0x68 (default for MPU6050)
```

---

## Connection Diagram - NRF24L01+ Module

```
┌──────────────────────────┐
│   NRF24L01+ RF Module    │
├──────────────────────────┤
│                          │
│  VCC ────→ 3.3V + 10µF cap
│  GND ────→ Common ground
│                          │
│ ┌─ Antenna (PCB or wire)
│                          │
│ SPI Connections:         │
│  CSN  ────→ PB0  (CS)    │
│  CE   ────→ PB1  (Enable)│
│  SCK  ────→ PA5  (Clock) │
│  MOSI ────→ PA7  (Data)  │
│  MISO ────→ PA6  (Data)  │
│  GND  ────→ GND          │
│                          │
│ Optional:                │
│  IRQ  ────→ PB4 (unused) │
│                          │
└──────────────────────────┘

⚠️ CRITICAL: Decoupling Capacitor
VCC ──[10µF capacitor]──→ GND
(Place close to NRF module!)

SPI Configuration:
- Frequency: 8MHz
- Mode: SPI MODE 0
- CSN active: LOW
- CE for RX/TX: HIGH
```

---

## Connection Diagram - ESC & Motors

```
Each ESC Connection:
┌──────────────────────┐
│   30A ESC (BLHELI)   │
├──────────────────────┤
│                      │
│ Signal ──→ PA0, PA1, PA2, or PA3 (PWM)
│ +5V (BEC)─→ 5V supply (or use separate BEC)
│ GND ──────→ Common ground
│                      │
│ Motor Wires:         │
│  Phase A ─→ Motor terminal
│  Phase B ─→ Motor terminal
│  Phase C ─→ Motor terminal
│                      │
│ Battery +──→ Main power
│ Battery -──→ GND
│                      │
└──────────────────────┘

Motor Connections:
┌─────────────────────────┐
│   RS2205 2300KV Motor   │
├─────────────────────────┤
│                         │
│ Phase A ←─ ESC out      │
│ Phase B ←─ ESC out      │
│ Phase C ←─ ESC out      │
│ (Any order ok, reverses  │
│  motor direction if needed)
│                         │
└─────────────────────────┘

Power Distribution:
        ┌─────────────┐
        │   Battery   │
        │  (3S LiPo)  │
        └─────────────┘
              │
        ┌─────┴─────┐
        │           │
       JST      PDB (Power
       XS60    Distribution)
        │           │
    ┌───┴────┬──────┴───┬─────┐
    │        │          │     │
   ESC1    ESC2      ESC3   ESC4
   │        │          │     │
   M1       M2         M3    M4
```

---

## Complete System Wiring Overview

```
    ╔════════════════════════════════════════════╗
    ║    STM32F411 BLACK PILL (MAIN)            ║
    ║    Flight Controller                       ║
    ╚════════════════════════════════════════════╝
                     │
        ┌────────────┼────────────┐
        │            │            │
        ↓            ↓            ↓
    
    GPS        MOTORS     RADIO/NRF
    │          │          │
    │          │          │
   [GPS]    [ESC1-4]    [NRF24L01+]
    │       [Motors]     [Receiver]
    │          │          │
    └────────┬─┴──────────┴─┐
             │              │
            UART           SPI
         (38400 baud)    (8MHz)
             │              │
        ┌────┴─┐        ┌────┴─┐
        │      │        │      │
       TX    RX       CLK    CS
       PA9   PA10    PA5    PB0
                     MOSI  MISO
                     PA7   PA6


    ╔════════════════════════════════════════════╗
    ║    POWER SYSTEM                            ║
    ║    3S LiPo Battery (11.1V - 12.6V)        ║
    ╚════════════════════════════════════════════╝
             │
          [PDB]
             │
    ┌────────┼────────┐
    │        │        │
   ESC1    ESC2   Aux Power
    │        │    (for STM32)
    M1       M2
    │        │
    ├────────┤
    │        │
   ESC3    ESC4
    │        │
    M3       M4
```

---

## Quadcopter Motor Configuration

```
        Front
          ↑
          │
    2 ────┼──── 1
    │     │     │
    │   STM32   │
    │     │     │
    3 ────┼──── 4
          │
          ↓
        Rear

Motor Spin Directions (X-config):
  M1 (FR): Clockwise (CW)    ↻
  M2 (RL): Counter-CW (CCW)  ↺
  M3 (FL): Counter-CW (CCW)  ↺
  M4 (RR): Clockwise (CW)    ↻

ESC-Motor Wiring:
  ESC1 → PA0 (STM32) → Motor 1 (FR)
  ESC2 → PA1 (STM32) → Motor 2 (RL)
  ESC3 → PA2 (STM32) → Motor 3 (FL)
  ESC4 → PA3 (STM32) → Motor 4 (RR)

Motor Phase Connections:
  (If motor spins wrong way, swap any 2 phases)
  
  Phase A ←─ Red wire
  Phase B ←─ Black wire
  Phase C ←─ Yellow wire
```

---

## Power Budget Analysis

```
Component Power Consumption:

STM32F411:
  - Idle: ~20mA
  - Full speed: ~100mA
  - Average: ~50mA
  ├─ Logic: 10mA
  ├─ Peripherals: 30mA
  └─ I/O: 10mA

GY-87 IMU:
  - Normal: ~5mA
  - Idle: ~2mA

NEO-7M GPS:
  - Active: ~70mA
  - Idle: ~30mA

NRF24L01+:
  - RX mode: ~13mA
  - TX mode (0dBm): ~11mA
  - Standby: ~0.26µA

Motors (Per Motor at hover):
  - ~2A average per motor
  - Peak: ~4A (full throttle)
  - 4 motors: ~8A average, ~16A peak

Total System at Hover:
  ├─ Controller: 0.05A
  ├─ Sensors: 0.08A
  ├─ Motors: 8.00A
  └─ Total: ~8.13A

Flight Duration Estimate:
  Battery: 1500mAh @ 8.13A avg
  Flight time: 1500mAh / 8130mA = ~11 minutes
  (Actual: 8-10 min with losses)
```

---

## Wiring Checklist

Use this checklist to verify all connections are correct:

```
GPS Module (NEO-7M):
  [ ] VCC → 3.3V
  [ ] GND → GND
  [ ] TX → PA10 (STM32 RX)
  [ ] RX → PA9 (STM32 TX)
  [ ] Antenna installed

IMU Module (GY-87):
  [ ] VCC → 3.3V
  [ ] GND → GND
  [ ] SCL → PC0
  [ ] SDA → PC1
  [ ] Mounting: on vibration dampers

Motors:
  [ ] Motor 1 → ESC1 → PA0
  [ ] Motor 2 → ESC2 → PA1
  [ ] Motor 3 → ESC3 → PA2
  [ ] Motor 4 → ESC4 → PA3
  [ ] All phase connections secure
  [ ] Motor spin directions verified

ESCs:
  [ ] Signal wires to PA0-PA3
  [ ] BEC 5V output to power rail
  [ ] Battery main power connected
  [ ] All GNDs connected to common ground

NRF24L01+ Receiver:
  [ ] VCC → 3.3V + 10µF capacitor
  [ ] GND → GND
  [ ] CSN → PB0
  [ ] CE → PB1
  [ ] SCK → PA5
  [ ] MOSI → PA7
  [ ] MISO → PA6
  [ ] Antenna installed
  [ ] Clean SPI connections

Battery:
  [ ] LiPo battery 3S (11.1V nominal)
  [ ] XT60 connector installed
  [ ] Balance connector for monitoring
  [ ] Main switch installed
  [ ] Fully charged before flight

General:
  [ ] All GND connections tied together
  [ ] No exposed solder bridges
  [ ] No wire shorts
  [ ] Tight connector seating
  [ ] No cold solder joints
  [ ] USB cable for programming
  [ ] ST-Link programmer connected (for initial flash)
```

---

## Troubleshooting - Visual Check

```
Problem: Motor won't spin

Visual Checklist:
  [ ] Signal wire connected to correct PWM pin?
  [ ] Motor phase connections all present?
  [ ] ESC has power light?
  [ ] Battery connected?
  [ ] All GND connections OK?
  
If all above OK:
  → ESC may be defective
  → Try different motor in same ESC
  → Try same motor on different ESC

Problem: NRF receiver not detected

Visual Checklist:
  [ ] All 5 SPI wires connected (SCK, MOSI, MISO, CSN, CE)?
  [ ] VCC has capacitor?
  [ ] Antenna attached?
  [ ] No bent pins?
  
If all above OK:
  → Reseat all connections
  → Try different NRF module if available

Problem: GPS not locking

Visual Checklist:
  [ ] Both UART wires connected (TX, RX)?
  [ ] Antenna attached and oriented UP?
  [ ] Module has power (red LED on)?
  [ ] Wires not crossed?
  
If all above OK:
  → Wait longer (5+ minutes for first lock)
  → Check for RF interference
  → Replace GPS module if defective
```

---

## Safety Considerations

```
⚠️ ELECTRICAL SAFETY:

1. Battery Handling:
   - LiPo batteries are fragile!
   - Never overdischarge (< 3V per cell)
   - Store in cool, dry place
   - Use proper charger
   - Check for swelling/puffing
   - Never leave charging unattended

2. Motor/ESC:
   - ALWAYS remove propellers when programming
   - Never power on ESC without props removed
   - Keep fingers away from spinning motors
   - Use arm-disarm safety feature

3. Soldering:
   - Wear safety glasses
   - Use proper ventilation
   - Keep solder away from eyes
   - Let connections cool before handling
   - Avoid cold solder joints

4. Testing:
   - Use current limiting power supply for first power-on
   - Start at lowest PWM values
   - Monitor for unusual behavior
   - Stop immediately if something seems wrong
```

---

## Revision History

```
Version 1.0 (2026-09-20)
- Initial schematic documentation
- Basic wiring diagrams
- STM32F411 pinout
- GPS, IMU, NRF, ESC connections
- Safety checklist

Version 1.1 (2026-09-21)
- Added power budget analysis
- Added troubleshooting section
- Added quadcopter configuration
- Added connection verification checklist
```

---

**Schematic created by:** [Your Name]  
**Last updated:** 2026-09-21  
**Status:** Active / Under Review

For questions about wiring, please check FAQ.md or create an Issue! 🔌

---
