
---
authors: Vrba, Saska
year: 2020
venue: IEEE RA-L (presented also at ICRA 2020)
link: https://mrs.fel.cvut.cz/icra2020-mav-detection
category: #detection #estimation
relevance: 🟢
---

# One-line summary
Compares single camera vs. stereo camera for spotting an uncooperative drone; single 
camera detects farther, but its distance-estimation method has a known weak point.

# What they built
- Sensor: Single (monocular) camera, tested head-to-head against a stereo depth camera 
  (Intel RealSense D435)
- Compute: NOT CONFIRMED — I have not verified what onboard hardware they used. 
  Do not assume Jetson/Intel without checking the actual paper.
- Architecture: CNN-based 2D bounding box detector (their earlier related work uses 
  a Darknet/YOLO-style detector — need to confirm this exact paper uses the same one)
- Distance method: Uses the size of the bounding box in the image to estimate distance — 
  smaller box = farther away (standard "pinhole camera" geometry trick)
- Real flight or simulation only?: CONFIRMED real flight tests. 
  "Swarm" / "highly controlled" — NOT CONFIRMED, don't assume these details.

# Key result (CONFIRMED from source)
- Single camera can detect the target beyond 30 meters
- Stereo depth camera's usable range is LIMITED TO roughly 20 meters 
  (this means accuracy degrades and becomes unreliable past that point — 
  NOT a hard "goes blind" cutoff, that's an overstatement)
- Their own error analysis found that most of the localization error comes from 
  imprecise DISTANCE estimation, not from failing to detect/locate the target 
  in the first place

# Limitation — split into confirmed vs. our own inference

**Confirmed limitation (stated by the paper):**
- Distance estimation accuracy is the main weak point of the whole system — 
  this is explicitly what their error analysis pointed to.

**Our inference — NOT directly stated in the paper, but reasonable engineering 
reasoning based on how size-based distance estimation works in general:**
- This method likely needs to assume the target's real-world physical size in 
  advance (bounding-box-size-to-distance math generally requires knowing the 
  object's true size). We have NOT confirmed the paper explicitly discusses 
  this as a limitation.
- If the target drone banks, tilts, or turns, its visible silhouette size could 
  shrink or distort, which could throw off the distance estimate. This is a 
  plausible and common failure mode for this type of method in general, but 
  we have NOT confirmed this specific paper tested or discussed this scenario.
- Both of the above should be treated as "things to test ourselves" rather than 
  "things this paper proved."

# Why it matters for us

**Hardware decision:** Reasonable evidence to lean toward single camera over stereo 
if long detection range matters more to us than precise distance accuracy. 
This is a real trade-off shown by the paper's confirmed numbers (30m+ vs ~20m), 
not a "stereo is bad, kill it" conclusion — stereo may still be worth reconsidering 
if our required range is under 20m and distance precision matters more than range.

**Software decision (flagged, not settled):** If we go with single camera, the 
paper's own distance-estimation approach (box-size-based) may be fragile for the 
reasons listed above under "our inference" — but since those are our own predictions 
and not confirmed findings, we should treat this as something to test in our own 
setup before committing to an alternative method (e.g., optical flow / time-to-collision, 
or bearing-only tracking) rather than ruling out their approach outright.

# What I have NOT verified — be aware before citing this note as fact
- Exact onboard compute hardware used
- Whether "swarm" or multi-drone context applies to this specific test
- Whether the paper explicitly names required-known-target-size or 
  orientation-change as tested limitations
- Exact detector architecture (assumed similar to their related earlier work, not confirmed identical)

# Related
[[TransVisDrone-2023]]
[[Zheng-2021-DetFly]]