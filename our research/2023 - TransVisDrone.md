
---
authors: Sangam, Dave, Sultani, Shah
year: 2023
venue: ICRA
link: https://arxiv.org/abs/2210.08423
category: #detection
relevance: 🟢
---

# One-line summary
A camera-based system to spot another drone in the sky, light enough to run onboard in real time.

# What they built
- Sensor: regular camera (not LiDAR)
- Compute: small onboard chip (Jetson Xavier NX)
- Real flight or simulation only?: Tested on recorded video + on real onboard hardware, not full live flight

# Key result
33 frames per second on a cheap onboard chip — fast enough for real-time use.
Better accuracy than older methods on 3 different test datasets.

# Limitation / what broke
Doesn't cover the "predict + steer" part at all — just spotting the drone in the image.
Tested on pre-recorded videos, not proven in an actual live chase scenario.

# Why it matters for us
This is the missing piece from Paper 1 if we want to use a camera instead of LiDAR.
Good real number to know: ~33 FPS is achievable on cheap onboard hardware — useful for planning what hardware we'd need to buy.

# Related
[[Pliska-2024-Interception]]
[[00-Interceptor-Papers-Overview]]