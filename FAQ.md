# FAQ - Frequently Asked Questions ❓

**Common problems and solutions for STM32-Based INAV Drone**

---

## 🚁 Flight & Control Issues

### Q1: Drone won't arm (red LED blinking)
**Answer:**
```
❌ Common Causes:
1. Battery voltage too low (< 8V for 3S)
   → Charge battery fully
   
2. Gyro calibration failed
   → Place drone on FLAT surface
   → Power on and wait 2 seconds
   
3. Receiver not connected
   → Check NRF wiring (SPI pins)
   → Verify UART baud rate 115200
   
4. Accelerometer not calibrated
   → Go to Configurator → Calibration
   → Click "Calibrate Accelerometer"
   → Place on level surface

✅ Fix:
- Power off
- Place drone on completely FLAT surface
- Power on, wait 3 seconds
- Try arming again (throttle down-left, Mode 2)
```

**CLI Check:**
```bash
status
# Should show: Armed: NO, GPS: OK (or SEARCHING), RX: CONNECTED
```

---

### Q2: Drone flies but unstable (shaking/wobbling)
**Answer:**
```
This is PID tuning problem!

❌ Too Much P (Proportional):
Symptoms: Oscillating, shaky flight
Fix: Reduce P value

✅ Step-by-step Fix:
1. Start with DEFAULT PIDs in INAV Configurator
2. Make small test flight (short hover)
3. If oscillating → reduce P by 2-5 points
4. Retry flight
5. If stable but slow response → increase P by 1-2

Example:
set p_roll = 38  # reduced from 40
set p_pitch = 38  # reduced from 40
set p_yaw = 80   # reduced from 85
save

6. Test flight again
7. Adjust I and D values after P is good
```

**Professional Tuning:**
```
PID Tuning Order:
1. P gain (response speed) - start conservative
2. D gain (dampening) - reduce oscillation
3. I gain (steady-state correction) - fine tune
```

---

### Q3: One motor doesn't spin or spins wrong direction
**Answer:**
```
❌ Motor won't spin at all:
1. Check motor connection to ESC
2. Test motor in Configurator:
   → Motor Tab → Spin motor at 1100
   → If no spin → physical connection issue
   
3. Check ESC is calibrated:
   → Move throttle to max → min → power cycle
   
4. Test ESC-motor cable connection
   → Swap motor phases (any 2 wires)
   → If now spins → ESC firmware issue

✅ Motor spins wrong direction:
1. Option A: Reverse motor
   → Swap any 2 motor wires
   
2. Option B: Reverse in firmware (advanced)
   → Edit INAV source code
   → Recompile and flash

For NRF setup, correct order is:
Motor 1: Front-Right (clockwise from above)
Motor 2: Rear-Left (clockwise from above)
Motor 3: Front-Left (counter-clockwise)
Motor 4: Rear-Right (counter-clockwise)
```

**Motor Testing via CLI:**
```bash
# Test motor 0 at 1050 (minimal thrust)
motor 0 1100

# Stop motor
motor 0 1000

# Test other motors
motor 1 1100
motor 2 1100
motor 3 1100
```

---

### Q4: Drone drifts in stable/angle mode
**Answer:**
```
Possible causes (in order of likelihood):

1. ❌ Accelerometer not calibrated
   → Configurator → Calibration → Calibrate Accel
   → Place on flat surface
   
2. ❌ Prop balance issue
   → Check props for cracks/damage
   → Try new propeller set
   → Balance props using prop balancer
   
3. ❌ Motor/ESC issues
   → One motor may be weaker
   → Test each motor individually
   → Check for wind resistance differences

4. ❌ Wind (if flying outdoors)
   → This is NORMAL for low-power drones
   → Increase I gain slightly to compensate
   → Or fly in calmer conditions
   
5. ❌ Vibration/motor imbalance
   → Check motor bearings
   → Ensure all fasteners are tight
   → Add vibration dampers if needed

✅ Quick Fix:
In CLI:
set i_roll = 35  # increase from 30
set i_pitch = 35  # increase from 30
save
Test flight → if better, keep it; if worse, revert
```

---

## 📡 Receiver & Radio Issues

### Q5: NRF receiver not detected / No signal
**Answer:**
```
❌ Step 1: Check Physical Connection
Verify NRF24L01+ wiring:
- VCC → 3.3V (with 10µF capacitor)
- GND → GND
- CSN → PB0 (Chip Select)
- CE  → PB1 (Chip Enable)
- SCK → PA5 (SPI Clock)
- MOSI → PA7 (SPI Data Out)
- MISO → PA6 (SPI Data In)

Use multimeter: Test continuity on each wire

❌ Step 2: Check INAV Configuration
In Configurator:
1. Go to Ports tab
2. Check UART1 or UART2 is set to "IBUS"
3. Baud rate: 115200
4. Click Save & Reboot

Via CLI:
get serialrx_provider
# Should show: IBUS

❌ Step 3: Check NRF Module
- Try different NRF module (if available)
- Check antenna is attached
- Verify NRF module is 3.3V version
- Some NRF modules are 5V only

❌ Step 4: Check Transmitter
- Verify transmitter is powered on
- Check transmitter IBUS firmware is updated
- Try binding process again:
  1. Power receiver alone
  2. Hold bind button on transmitter
  3. Wait for LED confirmation
  
✅ Quick Debug:
In CLI:
status  # Check if "RX Status: Connected"
channel # Show channel values
# If showing values → receiver working!
# If showing 0 values → transmitter not bound
```

**Common NRF Issues:**

| Issue | Solution |
|-------|----------|
| **No power light on NRF** | Check VCC connection, verify 3.3V |
| **Intermittent connection** | Add larger capacitor (100µF), check wire quality |
| **Range too short** | Improve antenna, move away from WiFi/Bluetooth |
| **Binding won't work** | Factory reset NRF, try different transmitter |
| **Channels inverted** | Check transmitter stick mapping, reverse in INAV |

---

### Q6: Receiver works but channels seem reversed
**Answer:**
```
Example: Left stick moves right instead of left

❌ Cause 1: Transmitter stick mapping wrong
Solution: Check transmitter settings (not INAV)

❌ Cause 2: INAV channel reversal needed
Solution in Configurator:
1. Go to Receiver tab
2. Check stick movements for each channel
3. If reversed, click "REVERSE" checkbox for that channel
4. Test stick again

❌ Cause 3: Wrong receiver mode
Solution: Ensure using Mode 2 or your preferred mode:
- Mode 1: Pitch/Yaw on left, Throttle/Roll on right
- Mode 2: Throttle/Yaw on left, Pitch/Roll on right (common)
```

---

## 🛰️ GPS & Navigation Issues

### Q7: GPS won't get a lock (keeps searching)
**Answer:**
```
Getting GPS lock takes TIME - be patient!

⏱️ First GPS lock timeline:
- Cold start (no fix data): 3-5 minutes
- Warm start (cached data): 30-60 seconds
- Hot start (recent fix): 10-20 seconds

❌ If still no lock after 5 minutes:

1. Check NEO-7M wiring:
   - VCC → 3.3V or 5V (check module specs)
   - GND → GND
   - TX  → PA10 (STM32 RX)
   - RX  → PA9 (STM32 TX)

2. Verify UART configuration:
   CLI:
   resource | grep SERIAL
   # Should show: SERIAL_TX 1 A09, SERIAL_RX 1 A10

3. Check baud rate:
   get serial_0_baudrate
   # Should be 38400 for NEO-7M

4. GPS antenna placement:
   - Mount antenna on TOP of drone
   - Keep away from motors (electrical noise)
   - Avoid metal objects nearby
   - Must have clear sky view

5. Reset GPS module:
   - Power off drone
   - Disconnect GPS
   - Wait 10 seconds
   - Reconnect and test again

✅ Signs of working GPS:
- LED on NEO-7M changes from red to green
- Configurator shows GPS info
- CLI: gps command shows valid coordinates
```

**GPS Verification:**
```bash
gps
# Output should show:
# GPS Fix: GPS_FIX_3D
# Satellites: 7+
# Latitude: [valid number]
# Longitude: [valid number]
```

---

### Q8: GPS locks but "Position Hold" mode doesn't work
**Answer:**
```
Position Hold depends on multiple systems working together:

Requirements:
✅ GPS lock (3D fix with 7+ satellites)
✅ Compass calibrated (if installed)
✅ IMU calibrated (accelerometer & gyro)
✅ Battery voltage stable
✅ Throttle stick in middle position

❌ Troubleshooting:

1. Verify GPS lock:
   CLI: gps
   # Should show GPS_FIX_3D and 7+ satellites

2. Compass calibration:
   Configurator → Calibration → Compass
   Rotate drone 360° in all directions slowly

3. IMU calibration:
   Configurator → Calibration → Accelerometer
   Place on completely flat surface

4. PID tuning for position hold:
   set pos_hold_p = 50
   set pos_hold_i = 40
   set pos_hold_d = 0
   save
   
5. Test in safe location:
   - Find open area (field, parking lot)
   - Do GPS lock check first
   - Switch to Position Hold mode
   - Apply small throttle to hover
   - Release sticks - drone should hold position
   - Test with gentle movements
```

---

## 🔋 Power & Battery Issues

### Q9: Battery drains too fast
**Answer:**
```
3S LiPo (11.1V) should give 8-12 minutes flight time

❌ If draining in < 5 minutes:

1. Check battery capacity:
   Configurator → should show mAh
   If less than 1000mAh → too small battery

2. Check motor KV:
   RS2205 2300KV is standard
   Higher KV = faster but more current draw

3. Check throttle setup:
   set motor_pwm_protocol = DSHOT600
   Verify ESC supports DSHOT, else use PWM

4. Reduce weight:
   Every 50g affects flight time
   Check for loose/unnecessary components

5. Balance props:
   Imbalanced props = more current
   Use prop balancer for all 4 props

✅ Battery health check:
- Fully charge battery
- Weigh it (1500mAh ≈ 260g)
- Check cell voltages are equal
- Look for puffing/swelling → REPLACE IMMEDIATELY
```

**Battery Monitoring in INAV:**
```bash
# In CLI:
status
# Shows battery voltage and current draw

# In Configurator:
Power & Battery section shows:
- Battery voltage
- Current draw
- Remaining capacity estimate
```

---

### Q10: "Low Battery" warning at full throttle
**Answer:**
```
This is voltage sag - NORMAL behavior!

Why it happens:
- Motors draw high current (5-10A at full throttle)
- Battery internal resistance causes voltage drop
- Configurator shows dropped voltage

❌ Not really low battery, but:
- Excessive sag means battery is old or low capacity
- Or wiring has resistance (loose connectors)

✅ Solutions:

1. Check battery connectors:
   - XT60 connector loose?
   - Tighten or replace

2. Check ESC connectors:
   - Verify all 3 battery wires connected
   - Look for corrosion

3. Use thicker wire:
   - Main battery power wire should be 10-12 AWG
   - Bad wire = high resistance = high sag

4. Use larger capacity battery:
   - 1500mAh instead of 1300mAh
   - Larger battery = lower internal resistance
   - Enables longer flights

5. Check battery age:
   - LiPo batteries degrade over 200+ charge cycles
   - Old batteries have higher resistance
```

---

## 🔌 Hardware Connection Issues

### Q11: Configurator can't connect to drone
**Answer:**
```
❌ Step 1: Check USB Connection
- Try different USB cable
- Try different USB port on computer
- Try another computer if available

❌ Step 2: Check USB Driver
Windows:
- Download ST-Link driver from STMicroelectronics
- Install driver
- Restart computer

Linux:
sudo apt install stlink-tools
sudo usermod -a -G dialout $USER  # Add user permission

macOS:
brew install stlink

❌ Step 3: Force DFU Mode (if stuck)
1. Power off STM32
2. Hold BOOT button
3. Plug in USB
4. Release BOOT button
5. LED should blink (DFU mode)
6. Try flashing firmware

❌ Step 4: Check if STM32 responds
CLI via another method:
Try UART over USB (if using converter)

✅ Power cycle test:
1. Unplug USB
2. Wait 10 seconds
3. Plug back in
4. Check for new COM port in Device Manager
5. Try Configurator again
```

---

### Q12: Motor won't stop (ESC beeping continuously)
**Answer:**
```
⚠️ DANGER - This is a serious issue!

❌ Immediate action:
1. DISCONNECT BATTERY IMMEDIATELY
2. Do NOT fly this drone
3. Check ESC

❌ Possible causes:

1. ESC not calibrated:
   - Disconnect battery
   - Set throttle to maximum
   - Connect battery (ESC beeps 2-3 times for calibration)
   - Ensure safety - props OFF
   - Return throttle to neutral
   - Wait for ESC ready beep

2. ESC signal wire disconnected:
   - Check signal wire from STM32 to ESC
   - Ensure PA0-PA3 motor pins are not damaged

3. Throttle stick not at minimum:
   - Move throttle stick to lowest position
   - Verify in Configurator Receiver tab

4. Defective ESC:
   - Test with known working motor
   - If still beeps → ESC is broken
   - Replace ESC

✅ Recovery process:
1. Disconnect battery
2. Remove props (SAFETY!)
3. Power on without battery, via USB only
4. In Configurator → Motor tab
5. Try setting motor to 1000 (minimum)
6. If responds → proceed carefully
7. If not responsive → ESC failure, replace it
```

---

## 📱 Software & Configuration

### Q13: Forgot my INAV CLI commands
**Answer:**
```
Just type "help" in CLI!

CLI command help:
```bash
help
# Shows all available CLI commands
# Scroll through list

help pid
# Shows specific command help

dump all
# Exports current configuration
# Save to text file as backup
```

---

### Q14: Can't find my STM32 in Device Manager (Windows)
**Answer:**
```
❌ Check USB cable:
- Try different cable
- Try different USB port

❌ Install ST-Link drivers:
1. Download: https://www.st.com/en/development-tools/st-link-v2.html
2. Click "Get Software"
3. Download "STLINK-V2 USB driver"
4. Extract and run installer
5. Restart computer

❌ Check BOOT pins:
- Ensure BOOT0 pin is LOW (connected to GND)
- If floating → may not enumerate

❌ Windows Device Manager:
1. Open Device Manager
2. Look for unknown device or "STM32"
3. Right-click → "Update driver"
4. Point to downloaded driver folder

✅ Verify connection:
Device Manager should show:
"STMicroelectronics STM32 STLink"
```

---

## ⚙️ Configuration & Settings

### Q15: How to reset drone to factory settings?
**Answer:**
```
❌ WARNING: This erases ALL your settings!

⚠️ Backup first:
In CLI:
dump all
# Copy-paste entire output to text file
# Save as "backup_settings.txt"

✅ Factory reset:
In CLI:
defaults
# Resets everything to factory defaults

save
# Apply reset

reboot
# Restart flight controller

⚠️ After reset, you'll need to:
1. Recalibrate accelerometer
2. Recalibrate compass
3. Reconfigure receiver (NRF IBUS)
4. Retune PID values
5. Configure GPS

Use your backup if you made good tuning!
restore from: cat backup_settings.txt in CLI
```

---

## 🚀 Advanced Troubleshooting

### Q16: Weird I2C/SPI communication errors
**Answer:**
```
I2C (GY-87 IMU) or SPI (NRF24) issues:

❌ Common I2C problems:
1. Loose wire connections
   → Check PC0 (SCL), PC1 (SDA)

2. Pull-up resistors missing
   → Add 4.7kΩ resistors on SDA and SCL
   → Between wire and 3.3V

3. Device address conflict
   → Check I2C scanner: multiple devices on same address?

4. Wire too long (capacity issue)
   → Keep I2C wires under 20cm

✅ I2C Test:
CLI: imu
# Should show valid readings
# If "Gyro: NOT FOUND" → I2C problem

❌ Common SPI problems (NRF):
1. CS pin (CSN) not toggling
   → Check PB0 connection

2. Clock frequency wrong
   → Keep SPI at 8MHz (default)

3. Wire order mixed up
   → Verify SCK, MOSI, MISO order

4. Power supply unstable
   → Add larger capacitor (100µF) to NRF VCC

✅ SPI Test:
Receiver should show in Configurator
If not → reseat all SPI wires
```

---

## 📞 Getting Help

**If you're stuck:**

1. **Check INAV Documentation:**
   https://github.com/iNavFlight/inav/wiki

2. **Search GitHub Issues:**
   https://github.com/iNavFlight/inav/issues

3. **Join INAV Community:**
   - RCGroups Forum
   - Discord Servers
   - Reddit: r/Multicopter

4. **For your specific drone:**
   - Check this project's Issues tab
   - Create detailed issue with:
     - What you tried
     - Error messages (screenshots)
     - Hardware configuration
     - Firmware version

---

**Still having issues? Create a GitHub Issue with all details!** 🆘

Good luck with your drone! 🚁✨
