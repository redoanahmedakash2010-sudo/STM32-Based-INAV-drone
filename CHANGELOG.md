# Changelog 📝

All notable changes to the STM32-Based INAV Drone project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2026-09-20

### Added
- Initial project release
- Complete hardware documentation
  - STM32F411 Black Pill flight controller
  - NRF24L01+ IBUS receiver integration
  - NEO-7M GPS module configuration
  - GY-87 IMU setup
  - 30A ESC motor control
  - RS2205 2300KV motor specifications

- Comprehensive documentation
  - README.md with full build guide
  - BUILDING.md - INAV firmware compilation
  - FAQ.md - Common questions and solutions
  - SCHEMATIC.md - Detailed wiring diagrams
  - FLIGHT_TEST_LOG.md - Flight testing template
  - CONTRIBUTING.md - Community contribution guidelines
  - CHANGELOG.md - This file

- Software support
  - INAV 6.0 firmware configuration
  - STM32F411 pin assignments (fixed)
  - NRF IBUS receiver setup (UART + SPI)
  - CLI command reference
  - PID tuning guidelines

- Bill of Materials
  - Complete component list
  - Cost breakdown (~$150-170)
  - Sourcing recommendations

- Safety documentation
  - Disclaimer and liability
  - Flying safety guidelines
  - Battery handling procedures
  - Electrical safety checklist

### Changed
- N/A (Initial release)

### Deprecated
- N/A (Initial release)

### Removed
- N/A (Initial release)

### Fixed
- N/A (Initial release)

### Security
- Added warning about battery safety
- Added caution about prop handling

---

## [1.1.0] - 2026-09-21 (Planned)

### Added
- Flight test results
  - Test 1: Stabilize mode (5:32 duration)
  - Test 2: Altitude Hold mode (7:15 duration)
  - Test 3: Receiver range test results
  - Performance metrics and observations

- Enhanced documentation
  - Extended FAQ with more troubleshooting
  - Power budget analysis
  - Quadcopter configuration diagrams
  - Decoupling capacitor specifications

- Hardware improvements
  - Vibration damper recommendations
  - Prop balancer usage guide
  - Cable management tips

- Software enhancements
  - Additional CLI command reference
  - PID tuning evolution tracking
  - Configuration backup procedures

### Changed
- Updated README with NRF receiver section
- Expanded motor testing section

### Deprecated
- Pre-release documentation (outdated)

### Removed
- N/A

### Fixed
- Clarified I2C pull-up resistor requirements
- Corrected GPS baud rate (38400)
- Fixed NRF power supply specifications

### Security
- Added battery monitoring guidelines
- Enhanced ESC safety procedures

---

## [1.2.0] - 2026-09-25 (Planned)

### Added
- Compass module support
  - HMC5883L integration guide
  - Calibration procedures
  - Position Hold mode enablement

- Position Hold testing
  - Test 4: Position Hold mode flight
  - Autonomous hovering results
  - GPS accuracy measurements

- Extended range testing
  - Test 5: Long-distance receiver range
  - Outdoor flight results
  - Signal strength mapping

- Hardware variants
  - Lightweight variant (~350g)
  - Heavyweight variant (extended flight time)
  - Budget build optimization
  - Racing configuration

- Video documentation
  - Assembly guide video outline
  - Flight test compilation
  - Troubleshooting demonstrations

### Changed
- Update BOM with optional compass
- Revise flight time estimates

### Removed
- Placeholder test sections

### Fixed
- N/A

### Security
- N/A

---

## [1.3.0] - 2026-10-01 (Planned)

### Added
- Autonomous waypoint missions
  - Waypoint programming guide
  - Mission planning tutorial
  - Return-to-Home function
  - Failsafe procedures

- Extended testing
  - Test 6: Waypoint mission execution
  - Test 7: Adverse weather conditions
  - Test 8: Outdoor long-duration flight (15+ min)

- Advanced configurations
  - Custom PID profiles for different conditions
  - Flying site recommendations
  - Seasonal adjustments

- Community contributions
  - User builds and modifications
  - International documentation
  - Video guides
  - Troubleshooting case studies

### Changed
- Expand CONTRIBUTING.md with more details
- Update performance benchmarks

### Removed
- Deprecated configuration files

### Fixed
- N/A

### Security
- Enhanced failsafe documentation
- Emergency procedures guide

---

## [2.0.0] - 2026-11-01 (Planned - Major Release)

### Added
- Firmware customization guide
  - Modifying target.h for different hardware
  - Compiling custom INAV firmware
  - Advanced PID tuning profiles

- Sensor expansion
  - Barometer for indoor flying
  - Additional compass options
  - Optical flow sensors
  - Obstacle avoidance guidance

- International versions
  - Spanish documentation
  - French documentation
  - Chinese documentation
  - German documentation

- Professional features
  - Telemetry logging
  - Data analysis tools
  - Performance visualization
  - Comparison tools

### Changed
- Major documentation reorganization
- Project structure updated
- New website/wiki

### Deprecated
- Old configuration formats
- Legacy firmware versions

### Removed
- Outdated troubleshooting entries
- Superseded tutorials

### Fixed
- N/A

### Security
- Enhanced security documentation
- Secure update procedures

---

## [3.0.0] - Future (Long-term vision)

### Added
- FPV capability
  - Camera mount recommendations
  - Video transmitter setup
  - FPV goggles guide

- Fixed-wing support
  - Plane configuration
  - VTOL hybrid aircraft
  - Glider tuning

- Multi-drone coordination
  - Formation flying guide
  - Swarm control
  - Mesh networking

- Web interface
  - Browser-based configurator
  - Real-time telemetry dashboard
  - Mission planning UI
  - Log analysis tools

### Changed
- Complete rewrite for modularity
- Microservices architecture

### Removed
- Legacy STM32F411 (move to STM32H7 for more power)

### Security
- Military-grade encryption
- Secure authentication

---

## Notes

### Versioning Scheme

- **MAJOR** (1.0 → 2.0): Significant project changes or breaking changes
- **MINOR** (1.0 → 1.1): New features added, backward compatible
- **PATCH** (1.0.1): Bug fixes, documentation updates, no new features

### How to Contribute to Changelog

1. Create a new section under "Unreleased"
2. Document your changes
3. When release is ready, move to version section
4. Follow format of existing entries

### Release Schedule

- **1.0.0**: 2026-09-20 ✅ Released
- **1.1.0**: 2026-09-21 (Pending)
- **1.2.0**: 2026-09-25 (Planned)
- **1.3.0**: 2026-10-01 (Planned)
- **2.0.0**: 2026-11-01 (Planned)
- **3.0.0**: Long-term (Future)

---

## [Unreleased]

### Added
- Flight test results from September 21
- Extended CLI command reference
- Power budget calculations

### Changed
- Improved README formatting
- Updated wiring diagrams

### Fixed
- Corrected I2C resistor values
- Clarified GPS module power requirements

### TODO
- [ ] Add compass module section
- [ ] Complete position hold testing
- [ ] Add video documentation
- [ ] International translations
- [ ] Community forum links
- [ ] Video guide scripts

---

## Legend

- 🎉 **New feature**
- 🐛 **Bug fix**
- 📖 **Documentation**
- ⚡ **Performance**
- 🔒 **Security**
- ⚠️ **Breaking change**
- 🚫 **Deprecation**

---

## Archives

### Version 0.9 (Development)
- Beta firmware testing
- Hardware prototyping
- Initial documentation

### Version 0.5 (Experimental)
- Proof of concept
- Component selection
- Framework design

---

## Contact & Support

**For questions about releases:**
- Create an Issue on GitHub
- Check existing Discussions
- Contact maintainers

**Reporting bugs:**
- Use Issues tab
- Provide version number
- Include error details
- Attach logs if applicable

---

**Last Updated:** 2026-09-20  
**Maintained by:** [Your Name]  
**License:** MIT

---

Keep track of project evolution! 📈
