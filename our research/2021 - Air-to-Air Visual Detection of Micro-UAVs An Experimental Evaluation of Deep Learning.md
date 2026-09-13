authors: Zheng, Chen, Lv, Li, Lan, Zhao
year: 2021
venue: IEEE RA-L
link: https://ieeexplore.ieee.org/document/9343737
category: #detection #dataset
relevance: 🟡

One-line summary
Created the Det-Fly dataset and ran a bake-off between 8 detection models to see what works best for air-to-air tracking.

What they built
* Sensor: regular camera (Created the "Det-Fly" dataset: 13,000+ real images of a drone chased by another drone).
* Compute: Benchmarked 8 existing computer vision models (both One-Stage like YOLO, and Two-Stage like Faster R-CNN).
* Real flight or simulation only?: Real photos for data collection, but evaluated offline.

Key result
* Two-Stage detectors (R-CNN: where we first take out all candidate objects and compare them to chek if they are drones) are highly accurate at spotting tiny, distant drones but are too slow for edge hardware.
* One-Stage detectors (YOLO) are fast enough for edge compute but struggle when the target is far away or blends into cluttered backgrounds (like mountains/trees).

Limitation / what broke
* Doesn't propose a new method or full interception pipeline — just tests existing perception models.

Why it matters for us
* If we train our own vision model, we need to download this exact Det-Fly dataset to train it.
* Useful reality check: Confirms we will likely have to use a One-Stage detector (like YOLO) for speed, but we must expect reliability to drop off at longer ranges.

Related
TransVisDrone-2023
00-Interceptor-Papers-Overview