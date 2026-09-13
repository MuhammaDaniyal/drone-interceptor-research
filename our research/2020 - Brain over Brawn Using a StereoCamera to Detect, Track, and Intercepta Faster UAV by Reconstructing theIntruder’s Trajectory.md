
---
authors: Barišić, Petric, Bogdan
year: 2022
venue: Field Robotics
link: https://arxiv.org/abs/2107.00962
category: #detection #guidance #testbed
relevance: 🟡
---

# One-line summary
Full pipeline using stereo camera; catches a FASTER target by predicting its repeating flight pattern (digit 8) and cutting it off, rather than chasing directly.

# What they built
- Sensor: stereo camera (ZED Mini)
- Compute: onboard
- Real flight or simulation only?: Both - simulation + real outdoor flight tests (9/12 successful catches)

# Key result
Successfully caught a target moving 30% faster than the interceptor itself,
by predicting the shape of its flight loop and cutting across to intercept.

# Limitation / what broke
Only works because the target flies a known, repeating pattern (figure-eight).
Won't work against a real evasive target trying to escape unpredictably.

# Why it matters for us
Shows a clever alternative to "just chase it" - useful IF our target behavior is predictable/repeating.
Probably not directly usable against a genuinely evasive rogue drone, but the general idea
("predict the pattern, don't just chase") might still combine with other papers' prediction math.

# Related
[[Pliska-2024-Interception]]
[[00-Interceptor-Papers-Overview]]