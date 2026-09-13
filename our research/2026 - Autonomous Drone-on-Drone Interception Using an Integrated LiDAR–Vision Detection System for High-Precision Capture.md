
**Year:** 2026
**Venue:** Drones journal (MDPI) — DOI 10.3390/drones10060420, open access
**Category:** #detection #hybrid-sensor #estimation
**Relevance:** 🟢

## Credibility flag
MDPI journal — generally considered looser peer review standards than IEEE/AIAA. 
Read claims here a notch more skeptically than other papers in our pile, particularly 
the specific accuracy numbers below, until cross-checked against a stronger source 
if this becomes central to our decision.

## One-line summary
Combines LiDAR (long-range coarse detection) with a camera (short-range precision 
confirmation) in a two-stage handoff, rather than choosing one sensor exclusively — 
an architecture pattern, not just a detection method.

## What they built
- Sensor: LiDAR (Livox brand, unconfirmed exact model from my sources) PLUS a 
  global-shutter camera (not standard rolling shutter — avoids motion distortion 
  at high closing speed)
- Compute: Jetson Orin Nano, connected via Ethernet (LiDAR) and running a 
  custom-trained YOLO model (camera)
- Communication: Jetson-to-flight-controller link over UART at 500 Hz
- Method: LiDAR point clouds processed via DBSCAN clustering (groups nearby points 
  into candidate objects) + Moving Horizon Estimation (tracks using a recent window 
  of past positions, not just the latest single reading) for long-range rough 
  detection/tracking. Camera + YOLO confirms target identity and refines position 
  as interceptor closes distance.
- Real flight or simulation only?: Real flight, multiple autonomous interception 
  missions completed (exact number not confirmed from available sources)
- Guidance: Proportional Navigation (PN) — CONFIRMED from source. Version (classic 
  vs. modified for re-engagement, like Pliska's fix) NOT confirmed — needs checking.
- Capture mechanism: Net-based — CONFIRMED from source. Hanging vs. launched 
  NOT confirmed.
## Key result
- Detection accuracy under 0.4m at ranges beyond 40m, verified against RTK-GPS 
  ground truth
- Target detection range up to 60m
- Over 90% detection rate from the camera-verification stage
- Multiple successful real autonomous interception missions

## Limitation / what broke (The Reality Check)
🔴 Requires BOTH LiDAR and camera onboard — heavier, more power-hungry, more 
expensive than a camera-only system. If our final decision lands on camera-only 
for SWaP-C reasons, this exact architecture doesn't directly transfer.

⚠️ MDPI venue — claims not yet cross-verified against a higher-scrutiny source. 
The specific accuracy numbers (0.4m, 60m, 90%) should be treated as "reported by 
authors" rather than "independently confirmed" until we check further.

⚠️ "Multiple successful missions" — exact number, target type (scripted vs. 
evasive), and full test conditions not confirmed from available sources. Need to 
check full paper before citing this as strong evidence of real-world robustness.

## Why it matters for us
Since we haven't ruled out LiDAR yet, this is a real candidate architecture worth 
weighing seriously — not just a discarded reference. The "long-range coarse sensor 
hands off to short-range precision sensor" PATTERN is valuable even independent of 
which exact sensors we pick — worth asking whether we could replicate this same 
handoff idea using two camera configurations (wide-FOV for acquisition, narrow-FOV/
zoom for terminal precision) if we do end up camera-only. The global-shutter insight 
applies regardless of final sensor decision — worth adopting either way, since 
rolling-shutter distortion at high closing speed is a real risk for ANY camera-based 
terminal guidance we build.

## Related
[[Marker-Less-Detection-2019]]
[[Pliska-2024-Interception]]