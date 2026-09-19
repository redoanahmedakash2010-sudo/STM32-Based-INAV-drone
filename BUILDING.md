# Building Custom INAV Firmware for STM32F411 🔧

This guide explains how to compile INAV 6.0 firmware from source and customize it for your STM32 Black Pill drone.

---

## ⚠️ When Do You Need to Build Custom Firmware?

**You need custom firmware if you want to:**
- ✅ Change motor/sensor pin assignments
- ✅ Add custom features
- ✅ Optimize performance
- ✅ Remove unnecessary features (save flash space)

**You DON'T need custom firmware if:**
- ✅ Just using CLI to tune PID
- ✅ Configuring receiver in Configurator
- ✅ Changing flight modes
- → Use **pre-built firmware** from INAV releases instead!

---

## 📋 Prerequisites

### Windows Setup
```
1. Install Git:
   https://git-scm.com/download/win

2. Install ARM GCC Compiler:
   https://developer.arm.com/downloads/-/gnu-rm
   (Version: arm-none-eabi-gcc 10.x or newer)

3. Install Make for Windows:
   Option A: MinGW (https://www.mingw-w64.org/)
   Option B: Use WSL2 (Windows Subsystem for Linux)

4. Install Python 3.x:
   https://www.python.org/downloads/
```

### Linux Setup (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install build-essential git python3
sudo apt install arm-none-eabi-gcc arm-none-eabi-binutils

# Verify installation
arm-none-eabi-gcc --version
```

### macOS Setup
```bash
# Install Homebrew first
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install ARM toolchain
brew install arm-none-eabi-gcc
brew install make python3

# Verify
arm-none-eabi-gcc --version
```

---

## 🚀 Step-by-Step Build Process

### Step 1: Clone INAV Repository
```bash
cd ~/Desktop  # or your preferred directory

# Clone the repository
git clone https://github.com/iNavFlight/inav.git
cd inav

# Check available branches/versions
git branch -a

# Checkout specific version (optional)
git checkout 6.0.0  # Use version 6.0.0
```

### Step 2: Configure for STM32F411

Navigate to target configuration:
```bash
cd src/main/target/STM32F411/
```

View the configuration file:
```bash
cat target.h
```

**Key file to modify: `target.h`**

---

### Step 3: Customize Pin Assignments (If Needed)

**Only do this if you changed your hardware wiring!**

Open `src/main/target/STM32F411/target.h` with your editor:

#### Example: Change Motor 1 from PA0 to PA5
```c
// BEFORE:
#define MOTOR1_PIN PA0

// AFTER:
#define MOTOR1_PIN PA5
```

#### Default Pinout (DO NOT CHANGE unless necessary):
```c
// Motor outputs (PWM)
#define MOTOR1_PIN PA0
#define MOTOR2_PIN PA1
#define MOTOR3_PIN PA2
#define MOTOR4_PIN PA3

// UART for GPS
#define SERIAL_USART1_TX_PIN PA9
#define SERIAL_USART1_RX_PIN PA10

// I2C for IMU
#define I2C1_SCL_PIN PC0
#define I2C1_SDA_PIN PC1

// SPI for NRF (if using SPI)
#define SPI1_SCK_PIN PA5
#define SPI1_MOSI_PIN PA7
#define SPI1_MISO_PIN PA6
#define SPI1_NSS_PIN PB0  // NRF CSN
```

#### Other Important Definitions:
```c
// Beeper
#define BEEPER_PIN PA8

// LED (status indicator)
#define LED0_PIN PC13

// Gyro
#define GYRO_SPI_BUS 0
#define GYRO_CS_PIN PA4

// Feature flags
#define DEFAULT_FEATURES (FEATURE_GPS | FEATURE_MOTOR_STOP)
```

### Step 4: Compile Firmware

Navigate back to root directory:
```bash
cd ~/inav  # Back to root
```

**Build for STM32F411:**
```bash
make TARGET=STM32F411
```

**Output:**
```
Compiling...
Linking...
✓ STM32F411_INAV_6.0.0.hex created!
```

**Compiled firmware location:**
```
build/STM32F411_INAV_6.0.0.hex
```

### Step 5: Verify Firmware Build

Check if hex file was created:
```bash
ls -la build/STM32F411_INAV_6.0.0.hex
```

Should show something like:
```
-rw-r--r-- 1 user staff 243KB Sep 19 12:34 STM32F411_INAV_6.0.0.hex
```

---

## 🔌 Flashing Firmware to STM32

### Option 1: Using INAV Configurator (Easiest)

1. **Connect STM32 to PC via ST-Link**
   - ST-Link GND → STM32 GND
   - ST-Link 3.3V → STM32 3.3V
   - ST-Link SWCLK → STM32 CLK
   - ST-Link SWDIO → STM32 DIO

2. **Open INAV Configurator**
   - Go to **Firmware Flasher** tab
   - Click **Load Firmware [Local]**
   - Select your compiled `.hex` file
   - Click **Flash Firmware**
   - Wait for completion

3. **Verify Flash**
   - Should show "Flashing complete!"
   - Status LED will blink

### Option 2: Using ST-Link Utility (Command Line)

**Windows:**
```bash
STM32_Programmer_CLI.exe -c port=SWD -w build/STM32F411_INAV_6.0.0.hex
```

**Linux/macOS:**
```bash
st-flash write build/STM32F411_INAV_6.0.0.hex 0x8000000
```

### Option 3: Using OpenOCD

```bash
openocd -f interface/stlink-v2.cfg -f target/stm32f4x.cfg \
  -c "program build/STM32F411_INAV_6.0.0.hex verify reset"
```

---

## ✅ Verify Successful Flash

1. **Check via Configurator:**
   ```
   Configurator → Setup → Should connect successfully
   ```

2. **Check via CLI:**
   ```bash
   version
   ```
   Should show your compiled firmware info.

3. **Check status:**
   ```bash
   status
   ```

---

## 🐛 Troubleshooting Build Issues

### Issue 1: "Command 'make' not found"
```bash
# Windows (MinGW)
Install MinGW from: https://www.mingw-w64.org/

# Linux
sudo apt install make

# macOS
brew install make
```

### Issue 2: "arm-none-eabi-gcc not found"
```bash
# Check if installed
arm-none-eabi-gcc --version

# If not installed:
# Windows: Download from ARM official site
# Linux: sudo apt install arm-none-eabi-gcc
# macOS: brew install arm-none-eabi-gcc
```

### Issue 3: Build fails with "Error in target.h"
```bash
# Check syntax
grep "define MOTOR" src/main/target/STM32F411/target.h

# Common mistake: Missing #define keyword
# ❌ MOTOR1_PIN PA0
# ✅ #define MOTOR1_PIN PA0
```

### Issue 4: Hex file too large (Flash overflow)
```bash
# This STM32F411 has 512KB flash
# If your build exceeds this, remove unused features:

# Edit Makefile:
# Remove unnecessary targets
# Reduce buffer sizes
# Disable unused sensors
```

---

## 📊 Build Configuration Reference

### Enabling/Disabling Features

Edit `src/main/target/STM32F411/target.h`:

```c
// GPS Support
#define USE_GPS
#define USE_GPS_UBLOX

// Compass/Magnetometer
#define USE_COMPASS
// #define USE_COMPASS_HMC5883  // Disable if not using

// Barometer
// #define USE_BARO  // Disable to save space

// LED
#define USE_LED_STRIP
// #define LED_STRIP_PIN PC5

// Debugging
// #define DEBUG_ENABLED  // Only for development
```

---

## 🔄 Useful Build Commands

```bash
# Full clean rebuild
make TARGET=STM32F411 clean
make TARGET=STM32F411

# Verbose output (see all compilation steps)
make TARGET=STM32F411 V=1

# Quick rebuild (without clean)
make TARGET=STM32F411

# View build size
arm-none-eabi-size build/STM32F411_INAV_6.0.0.elf

# Extract specific target info
make info-STM32F411
```

---

## 📝 Common Customizations

### Customization 1: Change Motor Spin Direction

In `src/main/target/STM32F411/target.h`:
```c
// Add motor reversal flags
#define MOTOR_DIRECTION_DEFAULT MOTOR_DIRECTION_NORMAL
// Or use MOTOR_DIRECTION_REVERSED for specific motors
```

### Customization 2: Adjust PID Defaults

In `src/main/flight/pid.c`:
```c
// Default PID values
pid[PIDROLL].P = 40;
pid[PIDROLL].I = 30;
pid[PIDROLL].D = 17;
```

### Customization 3: Change UART Baud Rates

In `src/main/target/STM32F411/target.h`:
```c
#define SERIAL_USART1_BAUDRATE 115200  // GPS serial
#define SERIAL_USART2_BAUDRATE 57600   // Other UART
```

---

## 🚨 IMPORTANT: Backup Before Modifying

**Always keep a backup of original target.h:**
```bash
cp src/main/target/STM32F411/target.h target.h.backup
```

If something breaks:
```bash
cp target.h.backup src/main/target/STM32F411/target.h
make clean
make TARGET=STM32F411
```

---

## 📚 Additional Resources

- **INAV Build Documentation:** https://github.com/iNavFlight/inav/wiki/Building-in-Docker
- **STM32F4 Technical Reference:** https://www.st.com/resource/en/datasheet/stm32f411re.pdf
- **ARM GCC Documentation:** https://gcc.gnu.org/

---

## 💡 Pro Tips

1. **Start with pre-built firmware** - Only build custom if needed
2. **One change at a time** - Modify single setting, build, test
3. **Keep git history** - Use `git diff` to track changes
4. **Document your changes** - Add comments in target.h
5. **Test incrementally** - Reflash and verify each change

---

## ⚠️ Final Checklist Before Flashing

- [ ] Firmware compiled successfully
- [ ] `.hex` file created in `build/` folder
- [ ] File size < 512KB (STM32F411 capacity)
- [ ] Changes are backed up
- [ ] STM32 connected via ST-Link
- [ ] USB cable connected to computer
- [ ] INAV Configurator is latest version
- [ ] No props on motors during testing

---

**Need help?** Check INAV GitHub Issues or contact the community!

Good luck with your build! 🚀
