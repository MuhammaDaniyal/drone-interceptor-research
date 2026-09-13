
---
authors: Yang, Quan
year: 2020
venue: ICRA
link: (search title if IEEE link is paywalled)
category: #guidance #detection
relevance: 🟢
---

# One-line summary
Steers the drone using the camera image directly (keep target centered + growing), 
skipping the "calculate exact 3D position" step entirely.

# What they built
- Sensor: forward-facing camera only
- Compute: not confirmed
- Real flight or simulation only?: not confirmed from abstract/figures alone — flag to check later

# Key result
Successfully intercepted a target using pure image-based reaction, no 3D math needed.

# Limitation / what broke
This "reactive" style struggles if the target briefly leaves the camera's view,
since there's no ongoing prediction to fall back on (unlike Paper 1's approach).

# Why it matters for us
This is a fundamentally different (and simpler) strategy than Paper 1.
Decision point for our project: do we want the "predict 3D position" approach (more capable, more math)
or the "just react to the image" approach (simpler, cheaper compute, but less capable at handling gaps)?

# Related
[[Pliska-2024-Interception]]
[[TransVisDrone-2023]]
[[00-Interceptor-Papers-Overview]]