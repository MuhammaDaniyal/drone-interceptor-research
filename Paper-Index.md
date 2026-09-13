# Design Choices — Master Reference
Purpose: quick lookup by category. Don't re-read paper notes — check here first.

---
## 🔍 DETECTION (spotting the target drone)

### TransVisDrone (Sangam, Dave, Sultani, Shah, 2023)
- **The Tech:** Single Camera + Edge AI model (CNN & Transformer).
- **The Proof:** Hit 33 FPS on a cheap, lightweight Jetson chip.
- **Use this for:** Proving that a camera-only, lightweight compute pipeline is completely viable for real-time onboard tracking.

### Air-to-Air Visual Detection of Micro-UAVs / Det-Fly dataset (Zheng, Chen, Lv, Li, Lan, Zhao, 2021)
- **The Tech:** 13,000+ real air-to-air images. Benchmarked 8 existing AI models (YOLO vs. R-CNN).
- **The Proof:** Fast models (YOLO) work on edge hardware but fail at long range. Heavy models (R-CNN) see far but are too slow for drones.
- **Use this for:** Downloading their open-source dataset to train our own AI, and setting realistic expectations about losing the target at long ranges.

### Onboard Marker-Less Detection and Localization of Non-Cooperating Drones (Vrba, Heřt, Saska, 2019)
- **The Tech:** Depth camera (not regular RGB) — finds the target by spotting isolated clusters of points floating in open space, gets full 3D position directly from the sensor, no box-size guesswork needed.
- **The Proof:** Marker-less, non-cooperative detection works reliably at short range with real 3D position data — no fragile distance math required.
- **Use this for:** Understanding WHY the field accepted the monocular box-size-guessing tradeoff (2020 paper) — this is the "accurate but short-range" side of that tradeoff, proven directly.

### Marker-Less MAV Detection and Localization (Vrba & Saska, 2020)
- **The Tech:** Hardware Bake-off: Single Camera vs. Stereo Depth Camera.
- **The Proof:** A single camera can track a target past 30+ meters. A stereo camera's usable range is limited to roughly 20 meters — accuracy degrades and becomes unreliable past that point (not a hard "goes blind" cutoff). (Warning: Their bounding-box depth math is flawed.)
- **Use this for:** Defending the hardware decision to prioritize a single camera over a heavy, short-range stereo camera.

### Brain over Brawn (Barišić, Petric, Bogdan, 2022) — detection portion only
- **The Tech:** Stereo Camera (ZED Mini) yielding native 3D coordinates.
- **The Proof:** Stereo cameras successfully extract precise 3D target coordinates without having to mathematically guess the enemy drone's physical size.
- **The Fatal Flaw:** 🔴 The interception LOGIC built on top of this detection (see Guidance section) assumes a predictable, repeating Figure-8 loop — but the detection itself works fine regardless of target behavior.
- **Use this for:** Understanding baseline stereo-vision detection capability, independent of their (flawed) guidance approach.

### A Real-to-Sim-to-Real Approach for Vision-Based Autonomous MAV-Catching-MAV (Ning, Zhang, Lin, Zhao, 2024)
- **The Tech:** Builds a simulated training environment out of real photos (not generic fake 3D scenery), then trains the detector entirely inside that simulation before deploying it on a real camera.
- **The Proof:** A detector trained this way transferred successfully to real outdoor flight tests against a real target drone, without needing thousands of real labeled flight images.
- **Use this for:** Solving the "not enough real training data" problem for mass production — a data-generation strategy, not a detection-model architecture to copy.

### **Autonomous Drone-on-Drone Interception Using an Integrated LiDAR–Vision Detection System for High-Precision Capture (2026)**

- **The Tech:** LiDAR handles long-range coarse detection (DBSCAN clustering + Moving Horizon Estimation tracking), camera with a global shutter confirms identity and refines position at close range — a two-stage handoff instead of picking one sensor.
- **The Proof:** Detection accuracy under 0.4m at ranges beyond 40m (verified against RTK-GPS ground truth), target detection out to 60m, 90%+ vision confirmation rate, multiple real autonomous interception missions completed.
- **Use this for:** Best documented example of a LiDAR+camera hybrid handoff architecture — long-range rough spotting → close-range precision. Also the origin of the "global shutter matters at closing speed" insight worth applying regardless of final sensor choice.
---

## 📐 ESTIMATION (figuring out speed/direction/distance from noisy readings)

### Towards Safe Mid-Air Drone Interception (Pliska, Vrba, Báča, Saska, 2024) — estimation portion
- **The Tech:** IMM filter — runs two backup guesses ("assume steady speed" and "assume steady turning") side by side, blends them based on which one matches reality better recently.
- **The Proof:** Found both simple assumptions break down against sharp/evasive maneuvers, and that their own drone's fast rotation during a chase adds extra tracking error most other papers ignore.
- **Use this for:** General-purpose motion estimator reference — but built and tested on LiDAR position data, not bearing-only camera data (see caveat in Guidance section).

### Observability-Enhanced Target Motion Estimation via Bearing-Box: Theory and MAV Applications (Zhang et al., 2026)
- **The Tech:** Uses the FULL bounding box (width + height over time), not just the center point, to mathematically separate target distance from target size — no need to know either in advance.
- **The Proof:** Also exploits the fact that multirotors can only accelerate by tilting, which removes the need for the fragile "assume constant velocity/acceleration" motion models. ⚠️ Venue/peer-review status and real-flight details NOT yet fully verified — abstract-level claims only.
- **Use this for:** Best current candidate for solving bearing-only estimation on a monocular camera WITHOUT knowing target size and WITHOUT needing a curving flight path.

---

## 🎯 GUIDANCE (deciding which way to fly / how to catch)

### Towards Safe Mid-Air Drone Interception (Pliska, Vrba, Báča, Saska, 2024) — guidance portion
- **The Tech:** Blends two guidance styles with an adjustable weight: fast-reacting pure pursuit early in the chase, smoother Proportional Navigation (PN) for precision near the target. Also offers an alternative MPC (Model Predictive Control) option that plans several steps ahead while respecting real drone speed/turning limits.
- **The Proof:** Fixed a known flaw in classic PN where the guidance math falsely reads "on course" both when about to collide AND when flying away after a miss — meaning older PN-based drones would fail to re-attempt after overshooting. Real flight tested, real net capture in ~2 seconds against a 5 m/s target.
- **Use this for:** Best guidance reference for a REUSABLE interceptor needing multiple attempts.
- **⚠️ CAVEAT:** The guidance formula itself just consumes a relative position + velocity vector, so it's technically sensor-agnostic — BUT it was only ever tested with LiDAR-quality position data. Feeding it monocular camera-derived estimates (e.g. from the Bearing-Box paper) is UNTESTED. Do not treat this as "camera-ready" until that combination is proven.

### An Autonomous Intercept Drone with Image-Based Visual Servo (Yang & Quan, 2020)
- **The Tech:** Reactive style — skips position/speed math entirely, just looks at the camera image and steers to keep the target centered and growing (getting closer), like keeping something in a crosshair.
- **The Proof:** Successfully intercepted a target using pure image reaction, no 3D math needed. (Real flight vs. simulation NOT fully confirmed from available sources.)
- **The Fatal Flaw:** If the target briefly leaves the camera frame, nothing to fall back on — no prediction exists to fill the gap.
- **Use this for:** Fast, human-piloted/unpredictable targets where short-term reaction matters more than long-term prediction.

### Interception of an Aerial Manoeuvring Target Using Monocular Vision (Jana, Tony, Bhise, Varun V.P., Ghose, 2022)
- **The Tech:** Pure pursuit guidance — keep the target's dot centered in the camera and steer toward it, no distance calculation at any point.
- **The Proof:** Real field-tested (ROS-Gazebo simulation, then real outdoor flight), keeps target in frame reliably, low miss distance.
- **The Fatal Flaw:** 🔴 Requires interceptor to be FASTER than target — no workaround within this method, since it never predicts, only reacts. Only proven at LOW target speeds; high-speed case is explicitly unfinished future work.
- **Use this for:** Simplest camera-only guidance proven to work — good low-speed baseline before adding distance-estimation complexity.

### Brain over Brawn (Barišić, Petric, Bogdan, 2022) — guidance portion
- **The Tech:** Predicts the shape of a repeating flight loop (figure-eight) and cuts across to meet the target rather than chasing directly — lets a slower interceptor catch a faster target.
- **The Proof:** Caught a target 30% faster than the interceptor itself, 9/12 successful real-world catches.
- **The Fatal Flaw:** 🔴 Only works if the target repeats a predictable pattern. A real evasive/human-piloted target won't cooperate — math completely breaks against a straight-line escape.
- **Use this for:** NOT our likely scenario, unless target behavior turns out to be genuinely repetitive (e.g., scripted patrol drone).

### Autonomous Drone-on-Drone Interception Using an Integrated LiDAR-Vision Detection System (2026) — guidance portion
- **The Tech:** Uses classic Proportional Navigation (PN) for the terminal approach, 
  once the LiDAR+camera pipeline has verified and localized the target.
- **The Proof:** Fed by high-precision position data (<0.4m accuracy at 40m+), part 
  of multiple successful real autonomous interception missions.
- **⚠️ CAVEAT:** Unconfirmed whether this is PLAIN PN or a modified version like 
  Pliska's fix for the "flying away after a miss looks identical to flying toward 
  for a collision" flaw. If it's unmodified PN, it likely inherits that same 
  re-engagement weakness — needs checking against the full paper.

---

## 🚁 FULL PIPELINE / END-TO-END (real testbeds — detect → estimate → guide → catch)

### Towards Safe Mid-Air Drone Interception (Pliska, Vrba, Báča, Saska, 2024)
- **The Tech:** Full detect → estimate → predict → guide → catch pipeline. Onboard 3D LiDAR detection, IMM estimation filter, blended PN/pursuit guidance, hanging net capture.
- **The Proof:** Real flight tested — real net capture in ~2 seconds against a 5 m/s target. No ground station steering during the chase.
- **Use this for:** Best overall pipeline BLUEPRINT — but every stage was built around LiDAR, not camera. Reusable as an architecture reference, not a drop-in system for our sensor.

### Brain over Brawn (Barišić, Petric, Bogdan, 2022)
- **The Tech:** Full pipeline, stereo-camera-based — detection + figure-eight trajectory prediction + interception.
- **The Proof:** Real flight tested, 9/12 successful catches, caught a target faster than itself.
- **The Fatal Flaw:** 🔴 Entire pipeline's guidance stage assumes a repeating figure-eight pattern — not applicable to an evasive real target (detection stage is fine, guidance stage isn't).
- **Use this for:** Reference for full-pipeline structure using stereo hardware; discard the guidance logic specifically.

----

## 🥅 CAPTURE MECHANISM (how the target is actually neutralized once reached)

### Hanging net — Towards Safe Mid-Air Drone Interception (Pliska et al., 2024)
- **The Tech:** A net physically hangs beneath the interceptor drone the whole flight (not fired/launched). Guidance flies the interceptor's body through the right position so the target flies into the net.
- **The Proof:** Real net capture, ~2 seconds, target moving 5 m/s. Chosen explicitly over a launcher because launched nets have limited attempts and a max safe firing speed.
- **Use this for:** Currently the only capture method in our pile with confirmed real-flight success against a maneuvering (non-scripted) target.

### Autonomous Drone with Ability to Track and Capture an Aerial Target — García, Caballero, González, Viguria, Ollero (2020), ICUAS, pp. 32-40, DOI 10.1109/ICUAS48674.2020.9213883
- **The Tech:** A dedicated "capture aerial robot" built for MBZIRC 2020 Challenge 1. 
  Exact capture mechanism (net, manipulator, or something else) NOT CONFIRMED — 
  paper is paywalled, only abstract retrieved. Full GNC (Guidance, Navigation, 
  Control) stack for tracking + balloon-bursting as a secondary task.
- **The Proof:** Tested in both simulation and real experiments (per abstract). 
  Exact success rate NOT confirmed.
- **The Fatal Flaw:** 🔴 Target flies a SCRIPTED figure-eight pattern (MBZIRC 
  competition rules, known in advance) — variable trajectory/speed, NOT hovering 
  as previously (incorrectly) noted here. Same scripted-pattern weakness as the 
  IISc and NimbRo MBZIRC papers already in our notes.
- **CORRECTION LOG:** Earlier version of this note incorrectly stated (1) net-launcher 
  mechanism — unconfirmed, likely conflated with a different paper, and (2) hovering 
  target — confirmed wrong, actual target moves on a variable trajectory. Fixed 
  after checking the actual abstract directly.
- **Use this for:** A fourth engineering team's approach to the same MBZIRC 2020 
  Challenge 1 scenario as IISc/NimbRo — useful for comparing GNC approaches across 
  teams, not as evidence of a distinct capture-mechanism category.

### Kinetic collision / destructive capture
- **Status:** 🔴 Essentially absent from peer-reviewed literature.
- **Why:** Requires single-digit-centimeter terminal accuracy — far tighter than net capture needs (net has physical size, tolerates sloppy aim). No vision-only system has demonstrated this accuracy against a moving target. Also a real safety/liability question (falling debris) nobody in the papers addresses.
- **If we're seriously considering this:** this isn't "find more papers," this is "we're proposing something the field hasn't solved yet" — needs to be an explicit conversation with the team, not an assumption.

### Net-based capture — Autonomous Drone-on-Drone Interception (2026)
- **The Tech:** Net-based capture (confirmed), fed by PN guidance once target is 
  localized to <0.4m accuracy.
- **The Proof:** Multiple real autonomous interception missions completed.
- **Not confirmed:** hanging net (like Pliska) vs. launched net — need to check 
  full paper.
- **Use this for:** Second confirmed real-flight example of net capture working, 
  alongside Pliska — worth comparing the two once "hanging vs launched" is 
  confirmed for this one.

### **A Concept for Catching Drones with a Net Carried by Cooperative UAVs (Rothe, Strohmeier, Montenegro, 2019)**

- **The Tech:** Two or more drones fly in coordinated formation, physically carrying a net stretched between them — the formation itself becomes the net's frame, rather than one drone scooping the target with a hanging net.
- **The Proof:** Not confirmed — paper is paywalled (IEEE Xplore, DOI 10.1109/ssrr.2019.8848973), no free access found. Could not verify test conditions, sensor setup, or real flight vs. simulation.
- **Use this for:** This is the ORIGIN paper of the whole MIDRAS project line — later papers cite it as establishing the core concept. ⚠️ Was not able to extract full details as paper was paywalled — treat everything above as secondhand info from citing papers only, not verified from source. Revisit if IEEE access becomes available.

### **Autonomous Multi-UAV Net Defense System for Aerial Drone Interception (Rothe, Strohmeier, Montenegro, 2025)**

- **The Tech:** Formation-carried net using a hybrid Leader-Follower + Virtual Structure control scheme, an adaptive controller (MRAC) that compensates for the net's unpredictable drag/weight effects on flight, and a physical shock-dampening mechanism with load sensing at the net attachment point.
- **The Proof:** Not confirmed — real flight vs. simulation, success rate, and target type unknown. Paywalled, abstract-only access.
- **Use this for:** The engineering maturation of the 2019 concept — most useful for the physical/mechanical side (dampening, load sensing) if we ever consider ANY net-based capture, even single-drone. ⚠️ Was not able to extract full details as paper was paywalled — verify before citing.
---

### ⚠️ GAP — no end-to-end paper fits us cleanly
No paper in our current pile is simultaneously: (1) monocular camera only, (2) edge-deployed with confirmed real-time FPS, (3) real-flight tested, AND (4) validated against a genuinely evasive, non-scripted target. Every candidate fails at least one of these four. This is a real gap in the published literature, not a hole in our reading — worth stating as a finding in our own report, not something to keep searching for.

---

## 🧮 FEASIBILITY ANALYSIS (can this even work, before we build it)

### Line-of-Sight-Constrained Multicopter Interceptability (Yang, Bai, Quan, 2025)
- **The Tech:** Calculates a "reachable bubble" for both interceptor and target based on speed/turning ability, checks if they can overlap — while ALSO factoring in the interceptor's limited camera field of view (target must stay visible the whole time, not just be physically reachable).
- **The Proof:** Produces a single "interceptability index" number — positive means catching the target is mathematically achievable if both sides play optimally, not-positive means it's impossible with that hardware, period, regardless of which guidance algorithm you use.
- **Use this for:** Sanity-checking our planned hardware specs (top speed, camera FOV, turning rate) against realistic target scenarios BEFORE spending money on building/testing a prototype. Answers "can we win this fight," not "how do we fly the chase."
- **Levers this gives us:** if the math says "not interceptable," only 3 things fix it — faster interceptor, wider camera FOV, or better turning ability.

---

## 🧠 DESIGN INSIGHTS (not tied to one paper — our own reasoning)

### Target behavior assumption
If target is human-piloted and evasive:
- Long-term path prediction = unreliable, can't out-guess a person
- Short-term (fraction-of-a-second) prediction = still valid — physics/momentum limits how fast a drone can change direction, regardless of pilot intent
- Favor reactive guidance (Yang & Quan style) over pattern-prediction tricks (Barišić style — not applicable to us)
- Re-engagement after a missed pass becomes critical (Pliska's fix directly addresses this)
- Priority for our system: reaction speed + clean recovery-after-miss > fancy long-range prediction

### Camera vs Stereo vs LiDAR (sensor choice)
- Single camera: longest detection range, weakest distance accuracy (Vrba & Saska)
- Stereo camera: better distance accuracy, shorter range (~20m usable) (Vrba & Saska)
- LiDAR: most accurate all-around, heavier/more expensive/more power draw (Pliska et al. — implied, not directly tested against camera in that paper)

### Pure Pursuit reality check (Jana et al. Robotica 2022)
- Requires interceptor to be FASTER than target — no exceptions
- Doesn't guarantee head-on approach — converges toward "arrive at current position," not "collide face-to-face"
- Proven at LOW target speeds only

---

## ❓ OPEN QUESTIONS FOR THE TEAM
- What's our actual expected target behavior — careless operator, patrol drone, or actively evasive?
- Abort/manual override strategy — who has the kill switch, what triggers it?
- Do we need RTK-GPS-style ground correction for our own drone's positioning, or can we tolerate regular GPS accuracy?
- Run interceptability math (Yang/Bai/Quan) against our ACTUAL planned hardware specs before committing further R&D time to specific guidance algorithms
- **BIGGEST UNRESOLVED GAP:** does ANY of our guidance methods (Pliska's blend, MPC, pure pursuit) actually work when fed bearing-only camera estimates instead of full 3D LiDAR/depth data? Nobody has tested this combination. This may be the actual core R&D question our project needs to answer, not something we can assume from existing papers.
- Once we have a distance/size estimate (from Bearing-Box or similar), do we still need it if pure pursuit guidance already works without it — or is distance needed separately just to trigger net/capture deployment at the right moment?