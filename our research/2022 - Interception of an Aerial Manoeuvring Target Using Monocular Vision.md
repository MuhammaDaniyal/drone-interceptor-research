
**Authors:** Jana, Tony, Bhise, Varun V.P., Ghose
**Year:** 2022
**Venue:** Robotica 40(12), 4535-4554
**Link:** https://www.cambridge.org/core/journals/robotica/article/interception-of-an-aerial-manoeuvring-target-using-monocular-vision/45EC80975FCD5727049599E55622B446
**Category:** #guidance
**Relevance:** 🟢

## One-line summary
Pure pursuit guidance using only a monocular camera — steer to keep the target's 
pixel centered and growing, with zero distance estimation used anywhere in the 
control loop.

## What they built
- Sensor: Monocular camera only, no depth/distance calculation of any kind
- Compute: NOT CONFIRMED — no onboard hardware or FPS number verified
- Method: "Relative velocity framework" — commands interceptor velocity so its 
  camera centerline rotates to point directly at the target's current pixel 
  position, driving the dot toward image center
- Real flight or simulation only?: BOTH — first tested in ROS-Gazebo (simulator), 
  then real outdoor field experiments

## Key result
Successfully keeps target inside the camera's field of view throughout the chase 
and achieves low miss distance — but ONLY for low-speed targets (confirmed, 
authors' own stated scope).

## Limitation / what broke (The Reality Check)
🔴 FATAL CONSTRAINT: interceptor MUST be faster than the target. This is pure 
pursuit — it reacts to current target position only, never predicts ahead, so a 
faster target simply cannot be caught by this method. No workaround exists within 
this approach.

🔴 Only validated at LOW target speeds. Authors explicitly list extending to 
high-speed targets as unfinished future work — do not assume this scales to a 
fast-moving threat.

⚠️ Does NOT guarantee a head-on collision geometry — approach angle depends on 
both paths' geometry, converges toward "arrive at target's current position," 
not "meet the target face-to-face." (Our own geometric reasoning, not something 
explicitly diagrammed in the abstract — worth confirming against the full paper.)

⚠️ Same fundamental guidance style (pure pursuit) that Pliska et al. (2024) 
explicitly used as a WEAK baseline comparison — they found it prone to 
overshooting and large miss distances, which is why they built a more advanced 
guidance law instead.

## Why it matters for us
This is the simplest camera-only guidance approach in our whole pile, and it's 
real-flight-tested — good starting baseline. But it directly conflicts with two 
things we need to resolve as a team: (1) do we know our target will always be 
slower than our interceptor, and (2) do we need high-speed capability, which this 
method hasn't proven. Also raises a genuine open question: if this method proves 
we don't NEED distance estimation to guide the drone at all, does that reduce how 
much we should invest in the Bearing-Box paper's distance/size estimation math — 
or do we still need distance for OTHER reasons (e.g., knowing when we're close 
enough to trigger capture/net deployment)?

## Related
[[Bearing-Box-2026]]
[[Pliska-2024-Interception]]
[[Tony-2021-MultiVehicle]]