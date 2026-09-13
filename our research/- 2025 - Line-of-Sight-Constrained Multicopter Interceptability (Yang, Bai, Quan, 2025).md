
**Authors:** Yang, Bai, Quan
**Year:** 2025
**Venue:** AIAA Journal of Guidance, Control, and Dynamics 48(4), 951-960
**Link:** DOI 10.2514/1.G008527 (paywalled — check institutional access or search for author preprint)
**Category:** #feasibility-analysis
**Relevance:** 🟢

## One-line summary
A mathematical feasibility calculator — checks whether an interceptor with given 
speed/turning/camera-FOV specs can EVER catch a given target, before any guidance 
algorithm is even chosen.

## What they built
- Sensor/hardware: N/A — this is theory + simulation, no physical drone built or flown
- Compute: N/A
- Method: Calculates a "reachable set" (all positions the interceptor could reach 
  soon, given speed + turning ability) and compares it against the target's own 
  reachable set. Explicitly folds in the interceptor's LIMITED CAMERA FIELD OF 
  VIEW as a hard constraint — target must stay visible throughout, not just be 
  physically reachable. Produces a single "interceptability index": positive = 
  interception achievable if both sides play optimally, not-positive = impossible 
  with that hardware regardless of guidance algorithm used
- Real flight or simulation only?: THEORY + SIMULATION ONLY. No real hardware, 
  no flight test. This is by design — it's a planning tool, not a flying system.

## Key result
Produces a usable pass/fail feasibility check for a PLANNED interceptor design, 
factoring in camera FOV limits (not just speed/maneuverability like older generic 
missile-interceptability math does).

## Limitation / what broke (The Reality Check)
🔴 ZERO real-world validation — pure math/simulation. The interceptability index 
is only as good as the assumptions fed into it (target speed, target maneuverability 
estimates) — garbage in, garbage out, if we don't have realistic target behavior data.

⚠️ Does not tell you HOW to intercept, only WHETHER it's possible. Still need a 
real guidance algorithm (from our Guidance category) even after running this — 
this paper doesn't replace that work, it just tells you if that work is worth doing 
for a given hardware spec.

⚠️ Assumes "both sides play optimally" for the positive-index case — a real target 
being flown carelessly or unaware of pursuit might be easier to catch than this 
worst-case math suggests, meaning a "not interceptable" result here could be overly 
pessimistic for less-sophisticated real threats.

## Why it matters for us
This should likely be run FIRST, before committing further R&D time to specific 
guidance/estimation algorithms. If our planned hardware specs (camera FOV, top 
speed, turning rate) come back as "not interceptable" against realistic target 
scenarios, that's critical information — it means we need to change the hardware 
plan (faster drone, wider FOV, better maneuverability) rather than just picking a 
smarter guidance algorithm, since no algorithm can beat physics. Directly connects 
to the "interceptor must be faster than target" hard rule we found in the pure 
pursuit paper (Jana et al.) — this paper generalizes that single rule into a full 
calculator covering more realistic scenarios.

## Related
[[Jana-2022-PurePursuit-Monocular]]
[[Pliska-2024-Interception]]