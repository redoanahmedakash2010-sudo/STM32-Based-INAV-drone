# STM32-Based INAV Autonomous Drone 🚁

A budget-friendly, custom autonomous drone build powered by an **STM32 Black Pill** flight controller running **INAV 6.0 firmware** with GPS navigation and telemetry capabilities.

## 🎯 Project Overview

This is a DIY autonomous drone project optimized for learning and experimentation. The build prioritizes cost-effectiveness while maintaining flight stability and autonomous navigation features through INAV firmware.

**What is INAV?**  
INAV (Intelligent Navigation) is a modern open-source autopilot firmware designed for fixed-wing aircraft, multirotors, and other UAVs. It provides autonomous flight modes, GPS navigation, and waypoint missions.

---

## ⚙️ Hardware Specifications

### Flight Controller
- **Microcontroller**: STM32F411 (BlackPill Board)
- **Firmware**: INAV 6.0
- **Programming Interface**: ST-Link V2
- **Features**: 32-bit ARM Cortex-M4, 512KB Flash

### Sensors & Navigation
| Component | Model | Purpose |
|-----------|-------|---------|
| **GPS Module** | NEO-7M | Position & altitude tracking |
| **IMU** | GY-87 (MPU6050) | 6-axis motion tracking |
| **Optional** | Compass Module | Heading reference |

### Propulsion System
| Component | Specification |
|-----------|---|
| **ESC** | 30A (BLHELI compatible) |
| **Motors** | RS2205 2300KV |
| **Quantity** | 4 (Quadcopter) |
| **Propellers** | 5.5" - 6" (to be confirmed) |

### Frame & Structure
- **Frame Type**: 3D Printed (initial prototype) → Carbon Fiber (planned upgrade)
- **Wheelbase**: ~250mm (standard racing quad size)
- **Weight**: ~400-500g (estimated)

### Power System
| Component | Specification |
|-----------|---|
| **Battery** | 3S LiPo (11.1V nominal) |
| **Capacity** | 1300-1500mAh (adjustable) |
| **Connector** | XT60 |

### Radio Control System
- **Receiver**: NRF24L01+ based IBUS Receiver (Custom DIY)
- **Transmitter**: NRF24L01+ based IBUS Transmitter (Custom DIY)
- **Protocol**: IBUS (FlySky FS-i6 compatible)
- **Frequency**: 2.4GHz ISM Band
- **Range**: ~300-500m (LOS, depends on antenna)

---

## 🔌 Wiring Diagram

```
STM32 Black Pill Pinout (Reference)
┌─────────────────────────────┐
│ USB  [microUSB]             │
├─────────────────────────────┤
│ GND ••• VCC                 │
│ GND ••• GND                 │
│ PB2 ••• RST                 │
│ PA9 (TX) → UART GPS         │
│ PA10(RX) ← UART GPS         │
│ PA0-PA3  → ESC1-4 (PWM)     │
│ PC0-PC1  → I2C (GY-87 IMU)  │
│ PA6-PA7  → Sbus/PPM Input   │
└─────────────────────────────┘

### Connection Details

**GPS (NEO-7M)**
- VCC → STM32 5V
- GND → STM32 GND
- TX  → PA10 (RX)
- RX  → PA9 (TX)

**IMU (GY-87)**
- VCC → STM32 3.3V
- GND → STM32 GND
- SDA → PC1
- SCL → PC0

**ESCs (Motor Control)**
- Signal → PA0, PA1, PA2, PA3 (PWM output)
- VCC → Battery positive (through PDB)
- GND → Common ground

**NRF24L01+ IBUS Receiver**
- VCC → STM32 3.3V or 5V (check module specs)
- GND → Common ground
- CE  → PB1 or configurable GPIO
- CSN → PB0 or configurable GPIO
- SCK → PA5 (SPI1 Clock)
- MOSI → PA7 (SPI1 MOSI)
- MISO → PA6 (SPI1 MISO)
- IRQ → Optional (PB4 for interrupt)

**IBUS Signal to STM32:**
- IBUS Data → PA6 or PA7 (UART/Serial input)
- GND → Common ground
```

---

## 📡 NRF24L01+ IBUS Receiver Setup Guide

### What is NRF IBUS Receiver?
- **Custom DIY receiver** using NRF24L01+ module
- **IBUS protocol** - compatible with FlySky FS-i6, FS-i10, etc.
- **Bidirectional communication** - telemetry capable
- **Budget-friendly** - NRF24 module costs ~$2-5

### NRF24 Module Connection (SPI + UART)

**Two connection methods:**

#### Method A: SPI + Serial (Recommended for INAV)
```
NRF24L01+ → STM32 Black Pill

Power:
- VCC → 3.3V (with decoupling capacitor 10µF)
- GND → GND

SPI (Hardware SPI1):
- CSN → PB0 (Chip Select)
- CE  → PB1 (Chip Enable)
- SCK → PA5 (Clock)
- MOSI → PA7 (Master Out)
- MISO → PA6 (Master In)

IBUS Signal Output:
- IBUS_OUT → PA10 (Serial RX alternate)
  OR
- IBUS_OUT → PA6 (if using software serial)

Optional:
- IRQ → PB4 (Interrupt - optional)
```

#### Method B: Parallel SPI (Direct to Configurator)
```
If your NRF receiver outputs IBUS on serial line:
- IBUS Serial Out → PA9 (UART1 TX) or PA10 (UART1 RX)
```

---

### INAV Configuration for NRF IBUS Receiver

**Via Configurator:**
1. Open **Configurator → Ports Tab**
2. Select **UART1** (or whichever UART gets IBUS signal)
3. Set Baud Rate to **115200**
4. Select **"IBUS"** from Serial RX dropdown
5. Click **Save and Reboot**

**Via CLI:**
```bash
# Configure UART for IBUS
set serial_0_functions = SERIAL_RX_ONLY
set serialrx_provider = IBUS

# Set baud rate
set serial_0_baudrate = 115200

# Save configuration
save
```

**Verify Reception:**
```bash
# Check receiver status in CLI
status

# You should see:
# RX Status: Connected
# RX Protocol: IBUS
# Signal Strength: Active
```

---

### Transmitter-Receiver Binding Process

**Assuming you have NRF IBUS transmitter:**

1. **Power up receiver alone** (no props on motors!)
2. **Transmitter should auto-scan** for receiver (LED blinks)
3. **Press bind button** on transmitter
4. **Receiver LED changes** (solid = bound)
5. **Test stick movement** in Configurator → Receiver Tab

**If binding fails:**
- Check NRF module antenna
- Verify SPI connections
- Try **manual frequency scan** on transmitter
- Check RF power settings (0dBm vs 6dBm)

---

### Typical NRF IBUS Channel Mapping

```
IBUS channels (14 channels total):
Channel 1 → Aileron (Roll)
Channel 2 → Elevator (Pitch)
Channel 3 → Throttle
Channel 4 → Rudder (Yaw)
Channel 5 → Mode selector
Channel 6-14 → Aux channels

In INAV Configurator:
- Set stick configuration (Mode 1/2)
- Verify all channels move
- Reverse if needed
```

---

### Common NRF IBUS Issues & Solutions

| Problem | Solution |
|---------|----------|
| **No signal in Configurator** | Check SPI wiring, verify UART baud 115200 |
| **Intermittent connection** | Add decoupling capacitor to NRF VCC, check antenna |
| **Range too short** | Upgrade antenna, reduce RF interference, increase TX power |
| **Binding won't work** | Factory reset NRF module, check transmitter firmware |
| **Channels reversed** | Set channel reversal in INAV Receiver settings |
| **Signal lag/delay** | Reduce loop rate in transmitter firmware, check serial baud |

---

### ⚡ Important Notes for NRF IBUS Setup

1. **Power Supply Critical**: NRF24 needs clean 3.3V power
   - Add 10µF capacitor near VCC pin
   - Use separate 3.3V regulator if possible
   
2. **SPI Speed**: Keep SPI clock at 8MHz (default for INAV)
   
3. **Antenna**: Proper antenna improves range dramatically
   - PCB antenna: ~100m range
   - External wire antenna: ~300-500m range
   
4. **Telemetry**: If transmitter supports telemetry, configure in INAV
   - Battery voltage feedback
   - GPS data
   - Attitude information

---



---

## 🛠️ Software Setup

### Prerequisites
- **INAV Configurator** (Windows/Mac/Linux)
- **ST-Link Driver** (for STM32 programming)
- **USB Cable** (micro-USB for Black Pill)
- Arduino IDE or STM32CubeIDE (optional, for custom firmware)

### Step 1: Flash INAV 6.0 Firmware

1. Download [INAV Firmware](https://github.com/iNavFlight/inav/releases) for STM32F411
2. Download [INAV Configurator](https://github.com/iNavFlight/inav-configurator/releases)
3. Connect STM32 via ST-Link to your computer
4. Open INAV Configurator → Firmware Flasher
5. Select the STM32F411 .hex file
6. Flash firmware
7. Wait for completion ✅

### Step 2: Configure INAV

1. Open INAV Configurator → Setup
2. **Calibrate Sensors**
   - Accelerometer (level surface)
   - Compass (rotate 360° in all axes)
3. **Configure Receiver**
   - Select PPM or SBUS input
   - Bind radio transmitter
4. **Motor Test**
   - Go to Motor Tab
   - Test each ESC individually (low throttle first)
5. **Flight Modes**
   - Setup autonomous modes (Angle, Horizon, Position Hold)

### Step 3: GPS Setup

1. In Configurator → Ports Tab
2. Set UART1 (PA9/PA10) to GPS (UBLOX)
3. Baud rate: 38400
4. Save & reboot
5. Wait for GPS lock (green LED on NEO-7M)

### Step 4: CLI Configuration (Advanced)

#### ⚠️ IMPORTANT - CLI Limitations on STM32F411

**What works via CLI:**
CLI (Command Line Interface) is useful for reading current configuration, but **hardware pin mapping CANNOT be changed via CLI on STM32F411**. The pin assignments are **hardcoded in firmware** during compilation.

#### CLI Access Method
```
1. Open INAV Configurator
2. Go to "CLI" Tab
3. Connected via USB
4. Type commands below
```

#### Useful CLI Commands (Read-Only)

**1. Check Current Pinout:**
```bash
resource
```
**Output Example:**
```
resource BEEPER 1 A09
resource MOTOR 1 A00
resource MOTOR 2 A01
resource MOTOR 3 A02
resource MOTOR 4 A03
resource SERIAL_TX 1 A09
resource SERIAL_RX 1 A10
resource I2C_SCL 1 C00
resource I2C_SDA 1 C01
```

**2. Get Motor Configuration:**
```bash
motor
```
**Output:**
```
motor 0 1050
motor 1 1050
motor 2 1050
motor 3 1050
```

**3. Check All Resources:**
```bash
# Get UART resources
resource | grep UART

# Get I2C resources
resource | grep I2C

# Get Motor resources
resource | grep MOTOR
```

**4. Flight Controller Info:**
```bash
status
```
**Shows:** CPU type, Memory, Firmware version, etc.

**5. Sensor Configuration:**
```bash
# Check IMU
imu

# Check Compass
compass_info
```

**6. GPS Information:**
```bash
gps
```
**Shows:** GPS fix type, satellites, coordinates

---

#### ❌ Commands That DON'T Work (And Why)

**❌ These will FAIL on STM32F411:**

| Command | Why It Fails |
|---------|------------|
| `resource MOTOR 1 A05` | Pin mapping is hardcoded in firmware |
| `resource UART 1 B00` | Port pin configuration is fixed |
| `resource I2C_SCL 1 B08` | Hardware I2C pins cannot be remapped via CLI |
| `resource BEEPER 1 A11` | Already assigned to other function |

**Reason:** STM32F411 has **limited GPIO flexibility**. Pin assignments are baked into the INAV firmware during compilation. CLI can only modify **parameters**, not **hardware mappings**.

---

#### ✅ What CAN Be Changed via CLI

You CAN modify these settings without recompiling:

```bash
# Gyro configuration
set gyro_sync_denom = 2

# PID tuning (Very important!)
set p_roll = 40
set i_roll = 30
set d_roll = 17

set p_pitch = 40
set i_pitch = 30
set d_pitch = 17

set p_yaw = 85
set i_yaw = 45

# Motor speed
set motor_pwm_protocol = DSHOT600

# Flight modes
set alt_hold_deadband = 40
set pos_hold_descentrate = 50

# Receiver settings
set rc_smoothing_type = INTERPOLATION
set rc_smoothing = ON

# Save all changes
save
```

---

#### 🔧 How to Actually Change Motor Pins (If Needed)

**If you REALLY need to change motor/sensor pins:**

### Method 1: Rebuild Firmware (Recommended for Advanced Users)
```bash
1. Clone INAV firmware: git clone https://github.com/iNavFlight/inav.git
2. Open file: src/main/target/STM32F411/target.h
3. Find motor pin definitions:
   #define MOTOR1_PIN PA0  → Change to PA5 (example)
4. Rebuild firmware using Make or STM32CubeIDE
5. Flash new firmware via ST-Link
```

**File Location Example:**
```
inav/src/main/target/STM32F411/target.h

Look for:
#define MOTOR1_PIN PA0
#define MOTOR2_PIN PA1
#define MOTOR3_PIN PA2
#define MOTOR4_PIN PA3

#define I2C1_SCL PC0
#define I2C1_SDA PC1

#define SERIAL_USART1_TX_PIN PA9
#define SERIAL_USART1_RX_PIN PA10
```

### Method 2: Use INAV Configurator Resource Remapper (Limited)
Some INAV targets support limited remapping:
```
Configurator → CLI → resource MOTOR 1 A05
```
**But this only works if STM32F411 target allows it** (most don't).

---

#### 📋 Default STM32F411 Black Pill Pin Assignment

| Function | Pin | Notes |
|----------|-----|-------|
| **Motor 1** | PA0 | PWM Output |
| **Motor 2** | PA1 | PWM Output |
| **Motor 3** | PA2 | PWM Output |
| **Motor 4** | PA3 | PWM Output |
| **UART1 TX (GPS)** | PA9 | Serial TX |
| **UART1 RX (GPS)** | PA10 | Serial RX |
| **I2C1 SCL (IMU)** | PC0 | Clock line |
| **I2C1 SDA (IMU)** | PC1 | Data line |
| **Radio RX** | PA6 or PA7 | PPM/SBUS input |
| **Beeper** | PA8 | Buzzer output |

---

#### 💡 Practical CLI Usage Tips

**1. Connect to CLI:**
```bash
# Via INAV Configurator
CLI Tab → Type commands → Press Enter
```

**2. Read Configuration Before Changing:**
```bash
diff all  # Shows all settings that differ from defaults
```

**3. Make a Backup:**
```bash
dump all  # Copy-paste output to text file for backup
```

**4. Test PID Changes:**
```bash
# Make one small change at a time
set p_roll = 42
save
# Test flight
# Adjust again if needed
```

**5. Reset to Defaults (if broken):**
```bash
# WARNING: This resets EVERYTHING
defaults
save
```

---

#### 🎓 Key Takeaway for Your Project

> **Your STM32F411 Black Pill has FIXED pin assignments. You CANNOT change motor pins, I2C pins, or UART pins via CLI. These are hardcoded. If you need different pins, you must rebuild and reflash the firmware.**

**Better approach:**
- ✅ Use the **default pinout** as designed
- ✅ Change PID values, flight modes, and parameters via CLI
- ✅ Only modify firmware pins if absolutely necessary
- ✅ Use `resource` command to **READ** current pin setup

---

#### 🛠️ Practical CLI Scenarios for Your DIY Drone

**Scenario 1: Checking your Setup (After first boot)**
```bash
# See what's connected
status

# Check motor pins
resource | grep MOTOR

# Verify NRF receiver UART
resource | grep SERIAL

# See GPS status
gps
```

**Scenario 2: Tuning PID for Stable Flight**
```bash
# Read current PID values
pid

# Make small adjustments (start conservative)
set p_roll = 38
set i_roll = 28
set d_roll = 16
save

# Fly test → if stable, increase slightly
# If oscillating, decrease P value
```

**Scenario 3: Configure NRF IBUS Receiver**
```bash
# Set UART for IBUS
set serial_0_functions = SERIAL_RX_ONLY
set serialrx_provider = IBUS
set serial_0_baudrate = 115200

# Check receiver channels
channel

# Save and reboot
save
```

**Scenario 4: Troubleshoot No Signal**
```bash
# Check if UART is configured
resource | grep SERIAL_RX

# Verify IBUS protocol is selected
get serialrx_provider

# Check if signal is being detected
status

# If "RX Status: OK" → problem is configuration
# If "RX Status: Searching" → physical connection issue
```

---

#### 📋 Common CLI Issues & Solutions

| Issue | CLI Command to Check | Solution |
|-------|---------------------|----------|
| **Motor 1 won't spin** | `motor 0 1200` | Check PA0 wire, ESC connection |
| **GPS not locking** | `gps` → shows "NO FIX" | Verify PA9/PA10 wiring, wait 30s |
| **NRF receiver not responding** | `status` → "RX Searching" | Check SPI wiring, UART baud rate |
| **I2C IMU not found** | `imu` | Verify PC0/PC1, I2C address |
| **PID tuning unstable** | Check `pid` values | Reduce P, increase I gradually |
| **Wrong motor direction** | `motor` command + reversing ESC | Use ESC programming or firmware rebuild |

---

#### 🚀 Full CLI Command Reference for Your Drone

```bash
# ═══════════════════════════════════════════════════════════
# SYSTEM INFORMATION
# ═══════════════════════════════════════════════════════════

status                    # Show FC status (RX, GPS, voltage, etc)
version                   # INAV firmware version
status                    # Flight controller health
build_info                # Compilation info

# ═══════════════════════════════════════════════════════════
# PINOUT & RESOURCES (READ-ONLY on STM32F411)
# ═══════════════════════════════════════════════════════════

resource                  # List ALL pin assignments
resource | grep MOTOR     # Show motor pins only
resource | grep UART      # Show UART pins
resource | grep I2C       # Show I2C pins
resource | grep SPI       # Show SPI pins

# ═══════════════════════════════════════════════════════════
# RECEIVER & RADIO
# ═══════════════════════════════════════════════════════════

channel                   # Show all 14 IBUS channels in real-time
get serialrx_provider     # Check receiver protocol
get rc_smoothing          # Check stick smoothing

# ═══════════════════════════════════════════════════════════
# MOTOR CONTROL
# ═══════════════════════════════════════════════════════════

motor                     # Show motor arm values
motor 0 1050             # Spin motor 0 at 1050 (test)
motor 0 1000             # Stop motor 0

# ═══════════════════════════════════════════════════════════
# SENSORS
# ═══════════════════════════════════════════════════════════

imu                       # Show IMU (GY-87) status
acc                       # Accelerometer values
gyro                      # Gyroscope values
compass_info              # Compass status
gps                       # GPS lock, satellites, coordinates
baro                      # Barometer data (if installed)

# ═══════════════════════════════════════════════════════════
# PID TUNING (Most Important!)
# ═══════════════════════════════════════════════════════════

pid                       # Show current PID values
set p_roll = 40          # Roll proportional gain
set i_roll = 30          # Roll integral gain
set d_roll = 17          # Roll derivative gain

set p_pitch = 40         # Pitch PID
set i_pitch = 30
set d_pitch = 17

set p_yaw = 85           # Yaw PID
set i_yaw = 45

save                      # Save changes (REQUIRED!)

# ═══════════════════════════════════════════════════════════
# FLIGHT MODES
# ═══════════════════════════════════════════════════════════

get max_angle_inclination # Max tilt angle
get alt_hold_deadband     # Altitude hold sensitivity

# ═══════════════════════════════════════════════════════════
# CONFIGURATION BACKUP
# ═══════════════════════════════════════════════════════════

dump all                  # Export entire config (copy to file)
get                       # Show all settings
diff all                  # Show only changed settings from defaults

# ═══════════════════════════════════════════════════════════
# DANGEROUS COMMANDS (Use with caution!)
# ═══════════════════════════════════════════════════════════

defaults                  # RESET ALL TO DEFAULT! (⚠️ Data loss)
save                      # ALWAYS do this after changes!
reboot                    # Restart flight controller
```



---

## 🚀 Assembly Instructions

### Frame Assembly
1. Prepare carbon fiber arms (or print frame parts)
2. Install motor mounts on each arm
3. Mount ESCs on arms using zip ties
4. Secure motors with set screws (align prop direction)
5. Mount landing gear

### Electronics Assembly
1. Create power distribution board (PDB) or use pre-made
2. Mount STM32 Black Pill on frame center
3. Connect GPS module on top (good sky view)
4. Mount GY-87 IMU on vibration dampers
5. Wire ESCs to motor outputs
6. Connect battery connector (XT60) via power switch

### Testing Checklist
- [ ] All motors spin in correct direction
- [ ] GPS acquires lock within 2-3 minutes
- [ ] IMU sensors calibrated
- [ ] Radio receiver responding
- [ ] Battery voltage reading correct
- [ ] No arm/disarm issues

---

## ✈️ Flying Your Drone

### Pre-Flight Checklist
```
☐ Battery fully charged
☐ GPS lock acquired (minimum 5 satellites)
☐ Compass calibrated
☐ Propellers secure & correct direction
☐ No loose wires or components
☐ Radio transmitter paired
☐ INAV Configurator shows stable gyro values
```

### First Flight
1. Start in **Stabilize Mode** (manual control, self-leveling)
2. Arm the drone (throttle down-left in Mode 2)
3. Gentle throttle increase to hover
4. Practice basic control
5. Once comfortable → try **Altitude Hold Mode**
6. Finally → **Position Hold Mode** (GPS hover)

### Autonomous Waypoint Mission
1. In Configurator → Mission Planner
2. Set waypoints on map
3. Adjust altitude & speed
4. Upload to drone
5. Switch to **Mission Mode** & arm
6. Increase throttle to start mission

---

## 🔧 Troubleshooting

| Issue | Solution |
|-------|----------|
| No GPS Lock | Check antenna placement, wait 5+ minutes, verify UART connection |
| Motors won't arm | Check battery voltage, verify radio calibration, check safety switch |
| Unstable flight | Re-calibrate accelerometer, check prop balance, reduce P gains |
| One motor not responding | Check ESC signal wire, verify motor connection, test with Configurator |
| Compass errors | Recalibrate compass, check for metal objects nearby, verify I2C connection |

---

## 📦 BOM (Bill of Materials)

| Item | Model | Qty | Cost |
|------|-------|-----|------|
| STM32 Black Pill | STM32F411 | 1 | $5 |
| GPS Module | NEO-7M | 1 | $15 |
| IMU | GY-87 | 1 | $8 |
| ESC | 30A Blheli | 4 | $20 |
| Motors | RS2205 2300KV | 4 | $30 |
| Frame | Carbon Fiber 250mm | 1 | $25 |
| Battery | 3S 1500mAh LiPo | 1 | $20 |
| Propellers | 5.5" Carbon | 8 | $15 |
| ST-Link | V2 Programmer | 1 | $5 |
| NRF24L01+ Module | 2.4GHz RF + Antenna | 1 | $3 |
| Decoupling Capacitor | 10µF (for NRF VCC) | 2 | $0.50 |
| **Estimated Total** | | | **~$150-170** |

---

## 📚 Learning Resources

- [INAV Official Documentation](https://github.com/iNavFlight/inav/wiki)
- [STM32 Development Guide](https://www.st.com/en/microcontrollers/stm32f4-series.html)
- [DroneForums Community](https://www.rcgroups.com/)
- [PID Tuning Guide](https://github.com/iNavFlight/inav/wiki/PID-Tuning)

---

## 🎯 Future Improvements

- [ ] Upgrade to carbon fiber frame
- [ ] Add barometer for altitude estimation
- [ ] Implement failsafe with parachute
- [ ] OSD (On-Screen Display) integration
- [ ] Long-range telemetry module (900MHz)
- [ ] Autonomous Return-to-Home
- [ ] Obstacle avoidance sensors
- [ ] Custom INAV firmware optimization

---

## 📜 License

This project is open-source under the **MIT License**. INAV firmware is licensed under **GPLv3**.

---

## 🤝 Contributing

Found an issue? Have an improvement?  
Feel free to:
- Open an **Issue** to report problems
- Submit a **Pull Request** with improvements
- Share your flight experience in Discussions

---

## ⚠️ Disclaimer

**Flying drones involves risks.** Always:
- Follow local drone regulations
- Fly in open, clear areas
- Never fly over people or property
- Wear safety gear (FPV goggles if using FPV)
- Start with manual control before autonomous modes
- Have a spotter during outdoor flights

**The author is not responsible for any damage or injuries.**

---

## 📞 Support

Have questions? Create an **Issue** on GitHub or check the INAV documentation.

**Last Updated**: September 2026  
**Status**: Active Development 🚀

---

**Happy Flying!** ✈️
