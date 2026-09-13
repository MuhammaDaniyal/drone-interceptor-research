
---
authors: Vrba, Saska
year: 2020
venue: IEEE RA-L
link: https://ieeexplore.ieee.org/document/8988144
category: #detection #estimation
relevance: 🟢
---

# One-line summary
Spots an uncooperative drone using a regular camera + estimates distance from how big it looks in the image.

# What they built
- Sensor: regular monocular camera (compared against a stereo depth camera)
- Compute: onboard CNN-based detector
- Real flight or simulation only?: Real flight tests with GPS ground truth for accuracy checking

# Key result
Camera-only detection works beyond 30m range; stereo depth camera maxes out around 20m.
But camera-only distance estimates are less precise than stereo's.

# Limitation / what broke
Most of their errors come specifically from imprecise distance estimation, not from
failing to spot the drone itself - the "how far is it" guess is the weak link.

# Why it matters for us
Direct camera-vs-stereo trade-off for our sensor choice:
- Camera alone = sees farther, but worse distance accuracy
- Stereo = more accurate distance, but shorter range
This is basically the sensor decision our whole project hinges on if going the camera route.

# Related
[[TransVisDrone-2023]]
[[Zheng-2021-DetFly]]
[[00-Interceptor-Papers-Overview]]