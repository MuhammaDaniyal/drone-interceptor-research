
# Design Choices — Master Reference

Purpose: quick lookup by category. Don't re-read paper notes — check here first.

---

## 🔍 DETECTION (spotting the target drone)

### TransVisDrone (Sangam, Dave, Sultani, Shah, 2023)
Uses a regular camera + AI model (CNN) onboard hardware (33 FPS on a small Jetson chip) rather than squeezing out maximum accuracy.
→ Good reference if: we want camera-only detection and care about hardware cost/weight.

### Air-to-Air Visual Detection of Micro-UAVs / Det-Fly dataset (Zheng, Chen, Lv, Li, Lan, Zhao, 2021)
Not a new method — a large dataset + fair comparison of 8 existing detection methods. Main finding: accuracy drops sharply as the target gets farther away or is viewed from certain angles.
→ Good reference if: we want to benchmark whatever detector we build against a realistic test set.

### Marker-Less MAV Detection and Localization (Vrba & Saska, 2020)
Regular camera + AI, but also estimates *distance* to target (not just "there it is") by using how big the target looks in the frame. Compared directly against a stereo depth camera.
→ Key trade-off found: single camera sees farther (30m+) but distance estimate is less precise. Stereo is more accurate but shorter range (~20m).
→ Good reference if: deciding between single camera vs stereo camera as our sensor.

### Brain over Brawn (Barišić, Petric, Bogdan, 2022) — detection portion only
Stereo camera detection, standard approach, works fine regardless of target behavior.
→ Note: the *guidance* trick in this same paper is listed separately below and downgraded — see Guidance section.

---

## 📐 ESTIMATION (figuring out speed/direction from noisy position readings)

### Towards Safe Mid-Air Drone Interception (Pliska, Vrba, Báča, Saska, 2024) — estimation portion
Uses an IMM filter — runs two backup guesses ("assume steady speed" and "assume steady turning") side by side, blends them based on which one matches reality better recently. Found that both simple assumptions break down against sharp/evasive maneuvers, and that their own drone's fast rotation during a chase adds extra error most other papers ignore.
→ Good reference if: we need a general-purpose motion estimator, not tied to a specific sensor.

---

## 🎯 GUIDANCE (deciding which way to fly / how to catch)

### Towards Safe Mid-Air Drone Interception (Pliska, Vrba, Báča, Saska, 2024) — guidance portion
**Predictive style.** Calculates the target's exact position + speed, predicts where it'll be next, aims there. Fixed a flaw in older missile-style guidance math where the drone would stop reacting properly after a missed pass — now it can miss and re-attempt.
→ Best fit for: reusable interceptor needing to try multiple times, general targets, camera OR LiDAR-based position estimate.

### An Autonomous Intercept Drone with Image-Based Visual Servo (Yang & Quan, 2020)
**Reactive style.** Skips position/speed math entirely — just looks at the camera image and steers to keep the target centered and growing (getting closer), like keeping something in a crosshair. Simpler, less compute needed.
→ Weakness: if target briefly leaves the camera frame, nothing to fall back on.
→ Best fit for: fast, human-piloted/unpredictable targets where short-term reaction matters more than long-term prediction (see Design Insight below).

### Brain over Brawn (Barišić, Petric, Bogdan, 2022) — guidance portion
Predicts the *shape of a repeating flight loop* (figure-eight) and cuts across to meet the target rather than chasing directly — lets a slower interceptor catch a faster target.
⬇️ Downgraded relevance: only works if target repeats a predictable pattern. A real evasive/human-piloted target won't cooperate.
→ Best fit for: NOT our likely scenario, unless target behavior turns out to be genuinely repetitive (e.g., scripted patrol drone).

---

## 🚁 FULL PIPELINE / REAL TESTBEDS

### Towards Safe Mid-Air Drone Interception (Pliska, Vrba, Báča, Saska, 2024)
Full detect → estimate → predict → guide → catch pipeline. LiDAR-based. Real flight tested, real net capture in ~2 seconds against a 5 m/s target.

### Brain over Brawn (Barišić, Petric, Bogdan, 2022)
Full pipeline, stereo-camera-based. Real flight tested (9/12 successful catches), but built around the figure-eight assumption above.

---

## 🧠 DESIGN INSIGHTS (not tied to one paper — our own reasoning)

### Target behavior assumption
If target is human-piloted and evasive:
- Long-term path prediction = unreliable, can't out-guess a person
- Short-term (fraction-of-a-second) prediction = still valid — physics/momentum limits how fast a drone can change direction, regardless of pilot intent
- Favor **reactive guidance** (Yang & Quan style) over **pattern-prediction tricks** (Barišić style — not applicable to us)
- **Re-engagement after a missed pass becomes critical** (Pliska's fix directly addresses this)
- Priority for our system: reaction speed + clean recovery-after-miss > fancy long-range prediction

### Camera vs Stereo vs LiDAR (sensor choice)
- Single camera: longest detection range, weakest distance accuracy (Vrba & Saska)
- Stereo camera: better distance accuracy, shorter range (~20m) (Vrba & Saska)
- LiDAR: most accurate all-around, heavier/more expensive/more power draw (Pliska et al. — implied, not directly tested against camera in that paper)

---

## ❓ OPEN QUESTIONS FOR THE TEAM
- What's our actual expected target behavior — careless operator, patrol drone, or actively evasive?
- Abort/manual override strategy — who has the kill switch, what triggers it?
- Do we need RTK-GPS-style ground correction for our own drone's positioning, or can we tolerate regular GPS accuracy?