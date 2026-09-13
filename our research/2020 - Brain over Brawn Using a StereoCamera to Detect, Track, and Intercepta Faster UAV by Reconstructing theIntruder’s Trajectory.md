
authors: Barišić, Petric, Bogdan
year: 2022
venue: Field Robotics (arXiv 2021)
link: https://arxiv.org/abs/2107.00962
category: #detection #guidance #testbed
relevance: 🔴 (Downgraded: Highly specific to predictable target paths)

# One-line summary
Uses a stereo camera and trajectory prediction to intercept a faster drone by cutting corners, but only works if the target flies in a repeating Figure-8 loop.

# What they built
- Sensor: Stereo camera (ZED Mini) which gives exact 3D coordinates without guessing drone size (but limited to close range).
- Compute: Full onboard processing.
- Architecture: Pattern-matching trajectory prediction. Instead of tail-chasing, it calculates the target's repeating flight path and flies to a future intersection point.
- Real flight or simulation only?: Both — real outdoor flight tests (9/12 successful catches).

# Key result
- Successfully caught a target moving 30% faster than the interceptor itself.
- Proves that "Lead Pursuit" (predicting the future and cutting off) allows a slower drone to defeat a faster one.

# Limitation / what broke (The Reality Check)
- The entire interception logic assumes the enemy is flying a highly predictable, repeating mathematical pattern (a Figure-8).
- If a rogue drone flies erratically, zig-zags, or just flies away in a straight line, this specific prediction algorithm will completely fail.

# Why it matters for us
- Software decision: This paper is proof for your lead that we MUST use predictive guidance (like Proportional Navigation) rather than just pointing our nose at the target (Pure pursuit). However, we must throw away their specific Figure-8 prediction code.
- Hardware context: Reinforces the Vrba paper's findings—a stereo camera gives you great precision but forces the intercept to happen at close range.

# Related
[[Vrba-2020-Markerless]]
[[Pliska-2024-Interception]]