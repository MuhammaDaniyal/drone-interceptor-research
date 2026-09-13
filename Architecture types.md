
**Type 1 — Fully Onboard Autonomous**  
Interceptor finds, tracks, and chases the target entirely with its own sensors. No ground station in the decision loop.  
_Papers:_ Pliska 2024 (LiDAR), Ning 2024 (monocular + laser ranger), Jana/Robotica 2022 (monocular), Yang & Quan 2020, Tony 2021.  
_Reality check:_ Almost all still depend on RTK-GNSS (needs a ground base station) for their **own** positioning, plus a safety/abort radio. "Fully autonomous" in these papers means the chase logic is onboard, not that the drone is infrastructure-free.

**Type 2 — Ground-Cued Handoff (Hybrid)**  
Ground radar / EO cameras / RF analyzers detect and classify the target first, hand off coordinates to the interceptor, which then flies out and uses onboard sensors only for terminal guidance.  
_Paper:_ Drones 2026 IDAS system (Livox LiDAR + global-shutter camera + Jetson Orin Nano, UART at 500 Hz to flight controller). Detection accuracy <0.4 m at >40 m, target detection out to 60 m.  
_This is how essentially every real deployed counter-UAS system actually works._

**Type 3 — Ground/GNSS-Dependent Formation**  
Multiple drones fly in formation carrying a shared net between them, positioned by centimeter-accurate GNSS, with centralized path planning.  
_Papers:_ Rothe, Strohmeier, Montenegro — SSRR 2019 and ICCRE 2025 (MIDRAS project).  
🔴 _Fatal flaw, stated by later reviewers:_ no onboard target detection at all — relied entirely on external sensor data, which proved insufficient for precise terminal guidance.

**Type 4 — RF / Soft-Kill (Non-Kinetic)**  
Jam or spoof the target's control link instead of physically catching it.  
_Paper:_ Souli, Kolios, Ellinas — RA-L 8(4), 2221–2228, 2023.  
_Not your category_ — different effect entirely, no physical capture.

**Type 5 — Multi-Interceptor Cooperative**  
Several interceptors triangulate the target between them, which sidesteps the bearing-only range problem (two cameras from different angles = real 3D position, no size-guessing).  
_Papers:_ Stasinchuk ICRA 2021, Zheng et al. Automatica 2025 (3× DJI M300 pursuers), Cooperative Bearing-Rate arXiv:2502.08089.  
_Cost-prohibitive for mass production, but note it's the cleanest fix to your monocular range problem._

---

### Separate axis: how they actually catch it

|Method|Papers|Viable for you?|
|---|---|---|
|Hanging net|Pliska 2024|✅ most proven|
|Launched net|García ICUAS 2020|⚠️ limited attempts, speed cap on safe launch|
|Formation-carried net|MIDRAS/Rothe|❌ needs multiple drones|
|Manipulator/grab|MBZIRC teams|❌ competition-specific|
|Kinetic hit-to-kill|**none**|❌ essentially absent from peer-reviewed literature|

---

### How many should you explore: 2

**Type 1 (fully onboard)** — your declared architecture. You've already got Pliska and Ning; add the Jana/Robotica 2022 one since it's monocular-only and open access.

**Type 2 (ground-cued hybrid)** — read **one** paper here, the Drones 2026 IDAS one. Skip the rest.

Skip Types 3, 4, and 5 entirely. Wrong effect, wrong cost, or wrong sensor topology for you.

---

### Why Type 2 deserves one read even though you're committed to onboard

Here's the strategic blind spot worth naming: your monocular camera detects a drone at roughly 30 m. That's fine for _chasing_ something you've already found. It's close to useless for _searching an entire sky_ to find it in the first place.

Every "fully autonomous" paper in your pile quietly starts the experiment with the target already in frame or with the interceptor pre-positioned nearby. None of them solve "a drone is somewhere over this facility, go find it." Ground radar/RF cueing is the answer everyone deploying for real reached for — not because their onboard autonomy was weak, but because initial detection and terminal guidance are genuinely different problems with different range requirements.

So the question for your team isn't "onboard vs ground-cued." It's: **who tells the interceptor where to fly before its camera can see anything?** That belongs in your Open Questions file — it may matter more to your system design than any guidance law you pick.