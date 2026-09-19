# Contributing to STM32-Based-INAV-Drone 🤝

Thank you for your interest in contributing to this project! This document provides guidelines for participating in the STM32-Based INAV Drone project.

---

## 📋 Code of Conduct

We are committed to providing a welcoming and inclusive environment. Please:
- Be respectful and constructive
- Welcome different perspectives
- Report unacceptable behavior to project maintainers
- Focus on improving the project

---

## 🚀 Ways to Contribute

### 1. **Report Bugs** 🐛
Found an issue? Please report it!

**How to report:**
1. Go to **Issues** tab on GitHub
2. Click **New Issue**
3. Use bug report template
4. Include:
   - Description of problem
   - Steps to reproduce
   - Expected vs actual behavior
   - Screenshots if applicable
   - Your hardware/software setup

**Example Bug Report:**
```
Title: Motors won't arm in Altitude Hold mode

Description:
When switching to Altitude Hold mode, the drone won't arm 
even though it arms fine in Stabilize mode.

Steps to reproduce:
1. Power on drone
2. Wait for GPS lock (LED solid green)
3. Switch to Altitude Hold mode
4. Try to arm (throttle down-left)

Expected: Drone arms successfully
Actual: Red LED blinks, won't arm

Environment:
- STM32F411 with INAV 6.0
- NEO-7M GPS
- NRF24L01+ receiver
- Battery fully charged (12.3V)
```

### 2. **Suggest Improvements** 💡
Have ideas for features or improvements?

**How to suggest:**
1. Go to **Discussions** (if available) or **Issues**
2. Create **Feature Request** issue
3. Describe:
   - What problem it solves
   - How it would work
   - Why it's important
   - Example use case

**Example Feature Request:**
```
Title: Add barometer support for better altitude stability

Description:
Currently using GPS for altitude, but in indoor testing 
GPS loses lock. A barometer would help maintain altitude 
even without GPS.

Proposed solution:
- Add BMP280 barometer support
- Use barometer for altitude in GPS-denied areas
- Configure in INAV Configurator

Benefits:
- Indoor testing capability
- Better altitude hold when GPS jammed
- Industry standard sensor (cheap & reliable)
```

### 3. **Improve Documentation** 📖
Help improve README, guides, and documentation!

**Contributions welcome:**
- Fix typos or unclear explanations
- Add new guides or tutorials
- Improve existing documentation
- Add translated versions
- Add diagrams or photos

**How to contribute documentation:**
1. Fork repository
2. Edit `.md` files in your local copy
3. Make changes
4. Test that formatting looks good
5. Submit Pull Request

### 4. **Share Flight Test Results** ✈️
Help others learn from your flight experiences!

**How to contribute:**
1. Complete flight test(s) following `FLIGHT_TEST_LOG.md`
2. Create new `.md` file: `FLIGHT_TESTS_[YourName]_[Date].md`
3. Include:
   - Flight conditions and results
   - PID tuning values
   - Issues encountered
   - Solutions found
4. Submit as Pull Request or upload to Discussions

**Template:**
```markdown
# Flight Test - [Your Name] - [Date]

## Flight Parameters
- Date: YYYY-MM-DD
- Location: [Where you flew]
- Duration: [Time in minutes]
- Mode: [Stabilize/Alt Hold/etc]
- Status: ✅ SUCCESS / ❌ FAILED

## Results
[Your test results and observations]

## PID Values Used
[Your tuning values]

## Issues Found
[Any problems during testing]

## Recommendations
[For next test or other users]
```

### 5. **Hardware Modifications** 🔧
Have hardware improvements or variants?

**How to contribute:**
1. Document your modifications clearly
2. Include:
   - Parts list (BOM)
   - Wiring changes (if any)
   - Code modifications (if needed)
   - Photos of your build
3. Create new file: `HARDWARE_VARIANTS.md`
4. Submit Pull Request

**Example:**
```markdown
## Variant: Lightweight Version (Budget Build)

### Changes from Original:
- Use cheaper 3D-printed frame
- Smaller 1300mAh battery
- 20A ESCs instead of 30A

### Bill of Materials:
[Updated BOM table]

### Performance:
- Weight: 350g (vs 450g original)
- Flight time: 6 minutes (vs 10 minutes)
- Benefit: Lower cost ($120 vs $160)

### Wiring:
[Same as original, no changes needed]

### Photos:
[Pictures of your build]
```

### 6. **Code Contributions** 💻
Contributing firmware modifications?

**Before you start:**
- Check Issues for requested features
- Discuss major changes first (create Issue)
- Follow project's coding style
- Keep changes focused and minimal

**Code contribution process:**
1. Fork repository
2. Create feature branch: `git checkout -b feature/add-compass-support`
3. Make changes
4. Test thoroughly
5. Commit with clear messages:
   ```bash
   git commit -m "Add compass calibration in CLI"
   ```
6. Push to your fork
7. Create Pull Request with description

---

## 📝 Pull Request Process

### Before Submitting:

1. **Fork the repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/STM32-Based-INAV-drone.git
   cd STM32-Based-INAV-drone
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make your changes**
   - Edit files
   - Test changes
   - Update documentation if needed

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "Describe your changes here"
   ```

5. **Push to your fork**
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Create Pull Request**
   - Go to GitHub
   - Click "Compare & pull request"
   - Fill out PR template
   - Submit!

### PR Description Template:

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New documentation
- [ ] Hardware variant
- [ ] Flight test results
- [ ] Code improvement

## Related Issues
Closes #[issue number]

## How to Test
Steps to verify the changes work

## Screenshots (if applicable)
[Add images if relevant]

## Checklist
- [ ] My changes follow the project's style
- [ ] I've updated documentation
- [ ] Changes are tested
- [ ] No new warnings/errors introduced
```

---

## 🐛 Bug Fix Contribution

**Steps to fix a bug:**

1. **Verify the bug exists**
   ```bash
   # Reproduce the issue
   # Document exact steps
   ```

2. **Find root cause**
   - Check relevant code/configuration
   - Read related documentation
   - Test hypothesis

3. **Implement fix**
   ```bash
   git checkout -b fix/issue-number
   # Make changes
   git commit -m "Fix: description of fix (fixes #issue-number)"
   ```

4. **Test thoroughly**
   - Verify bug is fixed
   - Ensure no new issues introduced
   - Test edge cases

5. **Submit Pull Request**
   - Reference original issue
   - Explain the fix
   - Provide test results

---

## 📚 Documentation Contribution

### Improving existing docs:
```bash
# Edit README.md, FAQ.md, etc
git checkout -b docs/improve-section-name

# Make changes
git add docs/
git commit -m "Docs: clarify GPS configuration section"
git push origin docs/improve-section-name

# Create PR
```

### Adding new guides:
```bash
# Create new file
nano docs/NEW_GUIDE.md

# Write guide following markdown standards
# Include code blocks, examples, images

git add docs/NEW_GUIDE.md
git commit -m "Docs: add guide for custom firmware building"
git push origin docs/new-guide
```

---

## 🎯 Areas Needing Help

Priority areas where contributions are especially welcome:

### High Priority:
- [ ] Indoor flight testing (no GPS) support
- [ ] Compass module integration guide
- [ ] Video/photo documentation
- [ ] Multilingual README files
- [ ] Performance optimization guides

### Medium Priority:
- [ ] Additional sensor support documentation
- [ ] Troubleshooting expanded
- [ ] Comparison with similar projects
- [ ] Alternative battery charger guides
- [ ] Motor/prop testing results

### Nice to Have:
- [ ] Community stories/builds
- [ ] Design improvements
- [ ] Alternative frame designs
- [ ] YouTube videos
- [ ] Community forum links

---

## 🏆 Recognition

Contributors will be recognized in:
- `CONTRIBUTORS.md` file
- GitHub contributors page
- Release notes for major contributions

Thank you for making this project better! 🙏

---

## ❓ Questions?

- **GitHub Issues:** Ask in project Issues
- **Discussions:** Use Discussions tab
- **Direct:** Contact maintainers

---

## 📜 License

By contributing to this project, you agree that:
- Your contributions will be licensed under MIT License
- You have rights to contribute the code/content
- You're not violating any third-party licenses

---

## 🚀 Getting Started

**First time contributing?**

1. Look for issues labeled `good first issue`
2. Read the code/docs you're modifying
3. Make a small change (typo fix, clarification)
4. Submit a Pull Request
5. Learn the process with low-risk contribution

---

## Development Setup

### Prerequisites:
```bash
# Clone repository
git clone https://github.com/redoanahmedakash2010-sudo/STM32-Based-INAV-drone.git

# Create development branch
git checkout -b develop

# Make changes
# Test locally
```

### Useful Commands:
```bash
# See your changes
git status

# See detailed changes
git diff

# Add files to staging
git add filename.md

# Commit with message
git commit -m "Descriptive message"

# See commit history
git log

# Push to remote
git push origin branch-name
```

---

## Testing Your Changes

### Documentation:
- [ ] Markdown renders correctly
- [ ] Links work properly
- [ ] Code blocks are formatted
- [ ] Images display correctly
- [ ] No spelling errors

### Code Changes:
- [ ] Code compiles without errors
- [ ] No new warnings introduced
- [ ] Logic is sound and tested
- [ ] Edge cases handled
- [ ] Documentation updated

### Flight Tests:
- [ ] Followed test protocol
- [ ] Results are reproducible
- [ ] Data is accurately recorded
- [ ] Issues clearly documented
- [ ] Recommendations provided

---

## 💬 Communication

Be respectful and constructive in all communications:
- Comment professionally on Issues/PRs
- Use positive language
- Respect others' time and effort
- Provide helpful feedback
- Welcome new contributors

---

## 🎓 Learning Resources

- [GitHub Guides](https://guides.github.com/)
- [Git Documentation](https://git-scm.com/doc)
- [Markdown Guide](https://www.markdownguide.org/)
- [INAV Documentation](https://github.com/iNavFlight/inav/wiki)

---

## 🏁 Contribution Checklist

Before submitting:
- [ ] Issue described clearly
- [ ] Changes tested
- [ ] Documentation updated
- [ ] Code follows project style
- [ ] Commit messages are clear
- [ ] No unrelated changes included
- [ ] Ready for review

---

**Thank you for contributing!** 🎉

Your efforts help make STM32-Based INAV Drone better for everyone! 

Keep building awesome drones! ✈️
