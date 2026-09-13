# Autonomous Drone-to-Drone Interceptor — R&D Literature Review

## What this is
Open literature research for an autonomous, vision-based counter-UAS interceptor. 
Goal of this vault: map what's proven, what's a "lab trick" that won't survive real 
deployment, and where the actual unsolved problems are — NOT a final recommendation. 
Sensor and guidance approach are both still open.

## Status (one paragraph, for someone with 30 seconds)
[X] papers reviewed across Detection, Estimation, Guidance, Capture, and Feasibility. 
No single sensor or guidance approach is settled. The biggest finding isn't in any 
one paper — it's a gap: no published system combines a lightweight camera-only 
sensor with a proven guidance law AND real-flight validation against a genuinely 
evasive target. Everything below explains why.

## Vault Map
- `papers/` — raw PDFs
- `our-research/` — one note per paper, format in `Template.md`
- `front-door.md` — index of every paper sorted by pipeline stage
- `Architecture-types.md` — interceptor classification + capture mechanisms
- `Summary.md` — head-to-head paper comparisons
- `first-draft.md` — early engineering blueprint (not a locked design)

## The Real Findings (read this section, skip the rest unless you want depth)

### Sensor tradeoff — genuinely unresolved
| Sensor | Proven capability | Real limitation |
|---|---|---|
| LiDAR | Full pipeline flight-proven (Pliska 2024) | Heavy, power-hungry, expensive — conflicts with mass-production SWaP-C goal |
| Monocular camera | Fast edge detection proven (TransVisDrone, 33 FPS on Jetson); longest range (30m+) | Distance estimation is fragile/unproven end-to-end — no known-size-free method has been flight-validated yet |
| Stereo camera | Clean 3D position, no size-guessing | Range capped ~20m; existing full-pipeline guidance built on it assumed a scripted flight pattern (fatal flaw) |
| Hybrid (LiDAR+camera) | One 2026 paper claims sub-0.4m accuracy at 40m+ | Needs both sensors onboard — doesn't solve SWaP-C, just relocates it |

### Guidance tradeoff — genuinely unresolved
- **Reactive (pure pursuit / IBVS):** proven simplest, real-flight tested, but requires interceptor to be FASTER than target, and only proven at low target speed
- **Predictive (Proportional Navigation / MPC):** more sophisticated, handles re-engagement after a missed pass (Pliska's fix) — but every proven version was tested with LiDAR-quality position data, never bearing-only camera estimates

### The single biggest open question in the whole field
**Nobody has published a test of a proven guidance law (PN, MPC, pursuit) fed by 
lower-quality, bearing-only camera estimates instead of LiDAR/depth data.** This 
isn't a paper we haven't found yet — it appears to be a genuinely unsolved 
combination. This is arguably the actual core R&D question, not something 
answerable by more reading.

### Capture mechanism — also unresolved, less explored than guidance
- Hanging net: only mechanism proven in real flight against a maneuvering target (Pliska 2024)
- Launched net, manipulator/grab arm: exist, but only tested against SCRIPTED competition targets (MBZIRC), not evasive ones
- Active mechanisms (e.g. rubber-ball launcher): one paper explicitly argues nets have poor fault tolerance to terminal attitude/velocity errors — a real counter-argument, not yet resolved either way
- Kinetic/destructive capture: essentially absent from peer-reviewed literature — requires accuracy no vision-only system has demonstrated

## Open Questions For The Team (decisions we can't make from literature alone)
- What's our actual expected target behavior — careless operator, patrol drone, or actively evasive? This changes which guidance approach even makes sense.
- Abort/manual override strategy — no paper addresses this for our context
- Run the interceptability feasibility math (Yang/Bai/Quan 2025) against our ACTUAL planned hardware specs before committing further R&D time to any one guidance algorithm
- Do we have appetite/budget to run the untested combination ourselves (camera estimates → existing guidance law), since literature won't answer it for us?

## How to go deeper
See `front-door.md` for the full paper-by-paper index, or `Summary.md` for direct comparisons.