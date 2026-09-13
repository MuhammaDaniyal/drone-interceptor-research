
---
Mainly focusing on guidance system.

authors: Pliska, Vrba, Báča, Saska
year: 2024
venue: IEEE RA-L
link: https://arxiv.org/abs/2405.13542
category: #guidance #estimation #testbed
relevance: 🟢
---

# One-line summary
Full pipeline for a drone to autonomously catch another drone in a net, tested for real.

# What they built
- Sensor: 3D LiDAR (not camera)
- Compute: onboard, no ground station steering
- Real flight or simulation only?: Both — 100 simulated scenarios + real net capture

# Key result
New guidance method catches target faster + more reliably than older methods.
Real capture took ~2 seconds. Target speed ~5 m/s.

# Limitation / what broke
Old guidance math fails to re-attempt after a missed pass.
Simple movement predictions (straight line / steady turn) break on sharp maneuvers.
Their own drone spinning fast during chase adds tracking error nobody else accounted for.

# Why it matters for us
Good blueprint for the full pipeline structure even though sensor differs.
Their guidance-math fix (for re-attempting after a miss) is sensor-independent — worth reusing.

# Related
[[00-Interceptor-Papers-Overview]]