
---
authors: Vrba, Heřt, Saska
year: 2019
venue: IEEE RA-L 4(4), 3402-3409
link: DOI 10.1109/LRA.2019.2927130
category: #detection
relevance: 🟡
---

# One-line summary
Depth-camera-based detection that gets full, accurate 3D position directly from 
the sensor — no distance-guessing math needed — but inherits the short-range 
limitation that comes with any depth/stereo camera.

# What they built
- Sensor: Depth camera (e.g. Intel RealSense-class stereo depth sensor)
- Compute: NOT CONFIRMED — no onboard hardware or FPS number verified
- Method: Finds isolated clusters of points in the depth map that are floating in 
  open space (disconnected from ground/large surfaces), checks the cluster's shape/
  size against expected drone dimensions at that measured distance to reject false 
  positives (birds, debris)
- "Marker-less, non-cooperating": target drone has NO tag/marker/light attached — 
  detection must work on a completely uncooperative, unmodified target
- Real flight or simulation only?: CONFIRMED real flight, onboard, live — explicitly 
  framed as the first stage of an interception system, not a standalone benchmark

# Key result
Proves marker-less, non-cooperative 3D localization works reliably — gets accurate 
distance directly from the sensor, avoiding the bounding-box-size guessing problem 
that breaks the 2020 monocular approach.

# Limitation / what broke (The Reality Check)
🔴 Inherits the standard depth/stereo camera range ceiling — same fundamental 
limitation flagged in the 2020 paper's stereo-vs-monocular comparison (~20m-class 
usable range, not confirmed as the exact number for THIS specific sensor/paper).

🔴 No edge-hardware/FPS validation found — same gap as most papers in this pile.

⚠️ This is the SHORT-RANGE, high-accuracy side of the exact tradeoff we already 
decided against when we picked monocular for long range. Don't confuse this with 
a candidate system — it's a documented reference point for why the tradeoff exists.

# Why it matters for us
Not a candidate for adoption (wrong side of our range-vs-accuracy decision), but 
valuable as evidence: confirms that ditching depth/stereo sensors for range reasons 
means we're deliberately trading away this kind of clean, no-guesswork 3D position 
data. Worth remembering if we ever add a SHORT-RANGE final-approach sensor on top 
of our long-range monocular camera — this paper is the reference for what that 
would buy us in the last few meters before impact/capture.

# Related
[[Marker-Less-MAV-Detection-2020]]
[[TransVisDrone-2023]]