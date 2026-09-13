
---
authors: Zhang, [2 others - not fully confirmed]
year: 2026
venue: NOT CONFIRMED — seen cited as IEEE T-RO by one source, unverified (treat as arXiv preprint until checked directly)
link: https://arxiv.org/abs/2601.06887
code: https://github.com/WindyLab/Bearing-Box
category: #estimation #bearing-only
relevance: 🟢
---

# One-line summary
Uses the full bounding box (not just its center point) to mathematically separate target distance from target size, and exploits multirotor flight physics (acceleration requires tilting) to drop the need for shaky constant-velocity/acceleration motion assumptions.

# What they built
- Sensor: Monocular camera (bounding box output from any standard object detector — no new sensor hardware needed)
- Compute: NOT CONFIRMED — no onboard hardware or FPS number verified
- Method: Models the bounding box's changing width/height over time as carrying distance AND size information simultaneously (not just bearing angle from the box center, which is what most bearing-only methods use)
- Multirotor-specific trick: Since a multirotor can only accelerate by tilting its body first, current acceleration reveals current tilt — used as extra information to replace generic constant-velocity/constant-acceleration assumptions
- Real flight or simulation only?: Paper claims "extensive experimental validation" and "real-world scenarios" — EXACT flight setup, hardware, target type NOT CONFIRMED from abstract alone. Need to open PDF to verify.

# Key result
Claims to solve the target-size-unknown problem (Vrba & Saska's flaw) AND the curving-flight-path requirement (Li et al.'s helical guidance conflict) simultaneously, using only data the detector already outputs for free.

# Limitation / what broke (The Reality Check)
🔴 Venue unconfirmed — cannot currently verify this cleared peer review at the level claimed (IEEE T-RO) vs. being an unreviewed preprint. Check paper header directly before citing it as peer-reviewed in any report.

⚠️ NOT YET VERIFIED CLAIMS: "real-world scenarios" and "extensive experimental validation" are from the abstract only — have not confirmed what hardware, what target, what range, or how many real flight tests were actually run. This is the single biggest thing to check before trusting this paper's conclusions.

⚠️ The multirotor-specific advantage (acceleration-requires-tilt trick) likely does NOT apply if the target is a fixed-wing drone or behaves in an unusual way outside normal multirotor flight dynamics. Paper's assumptions section not yet reviewed to confirm scope.

# Why it matters for us
On paper, this is the strongest fit yet for our exact constraints: monocular-only, no known-target-size requirement, no fighting-the-intercept-path requirement, and zero added sensor cost (reuses standard detector output).

BUT this is currently based on the abstract only — every specific claim above needs verification against the actual PDF before we treat this as our estimation approach. Code + video being public is a good sign for it being a real, testable claim rather than a vague theory paper.

# Next Steps
Open the actual PDF and confirm:
1. Venue / peer-review status
2. Real hardware used
3. Real flight test details — target type, range, speed
4. Does the "extensive validation" mean simulation, HITL, or genuine outdoor flight

# Related
[[Vrba-Saska-2020-MarkerLess]]
[[Li-2023-BearingOnly-Helical]]
[[TransVisDrone-2023]]