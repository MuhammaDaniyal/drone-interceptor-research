
Purpose: paragraph-form synthesis of every paper reviewed, for fast reading without 
opening individual notes. See front-door.md for category sorting, individual notes 
in our-research/ for full detail.

---

**Pliska, Vrba, Báča, Saska (2024) — Towards Safe Mid-Air Drone Interception**
This paper builds a complete system that lets one drone chase down and physically 
catch another drone in a hanging net, entirely on its own with no ground station 
steering it. It uses a LiDAR sensor to spot the target, an IMM filter (blending 
"assume steady speed" and "assume steady turning" models) to estimate position and 
velocity, and a blended guidance law — mixing fast-reacting pure pursuit early in 
the chase with smoother Proportional Navigation near the target — to steer toward 
it. Its main contribution is fixing a real flaw in classic PN guidance: the math 
can't distinguish "about to collide" from "just missed and flying away," meaning 
older PN-based interceptors would fail to re-attempt after overshooting. They also 
built an alternative MPC-based guidance option that directly accounts for the 
drone's physical speed/turning limits. Tested in ~100 simulated scenarios plus real 
flight, catching a moving 5 m/s target in about 2 seconds using a hanging net (chosen 
over a launched net for unlimited re-attempts and no speed cap on launch).
*Note: the guidance law is named FRPN in the published RA-L version but renamed EPN 
in arXiv v2 — same method, two names, worth knowing if searching for it elsewhere.*

**Sangam, Dave, Sultani, Shah (2023) — TransVisDrone**
Solves only the detection piece: spotting another drone using a regular camera and 
a small onboard computer chip, fast enough for real-time use. Combines a CNN 
(CSPDarkNet-53 backbone) for spatial features with a transformer (VideoSwin) for 
motion across frames. Achieved 33 FPS on a Jetson Xavier NX with no additional 
speed optimization (TensorRT), and reported higher accuracy than older methods 
across three datasets. Tested on recorded video and hardware speed benchmarks only 
— never in an actual live flight chase.

**Yang & Quan (2020) — An Autonomous Intercept Drone with Image-Based Visual Servo**
Proposes a fundamentally different, simpler steering strategy: instead of 
calculating the target's exact 3D position and predicting where to fly, it reacts 
directly to the camera image — steering to keep the target centered and growing 
larger (closer), like keeping something in a crosshair. Needs far less math than 
predictive approaches, but has a real weakness: if the target briefly leaves the 
camera's field of view, there's no fallback estimate to keep flying toward, unlike 
prediction-based methods. *Whether this was validated in real flight or simulation 
only was not confirmed from available sources.*

**Zheng, Chen, Lv, Li, Lan, Zhao (2021) — Air-to-Air Visual Detection of Micro-UAVs 
/ Det-Fly dataset**
Not a new detection method — a benchmark tool. The authors built the Det-Fly 
dataset (13,000+ real images of one drone photographing another in flight, varying 
background, angle, distance, altitude, lighting) and tested 8 existing detectors 
against it. *Corrected finding:* accuracy for every detector, one-stage and 
two-stage alike, drops as the target gets smaller/farther away — this is not a 
clean "two-stage sees far, one-stage doesn't" split. Grid R-CNN performed best 
across scales overall, while RefineDet and Faster R-CNN specifically degraded 
badly at small target size. Viewing angle also measurably affected accuracy for 
all methods. Most useful as a reality check (expect reliability to drop at range, 
regardless of detector choice) and as a ready-made test set for our own detector.

**Vrba & Saska (2020) — Marker-Less Micro Aerial Vehicle Detection and 
Localization Using Convolutional Neural Networks**
Provides a grounded hardware comparison between single and stereo cameras for 
detecting uncooperative drones. In real flight tests, a single camera detected 
targets beyond 30 meters, while a stereo depth camera's (Intel RealSense D435) 
usable range was limited to roughly 20 meters — accuracy degrades past that point, 
not a hard cutoff. To estimate distance with the single camera, the system used the 
size of the CNN's bounding box — a method the authors explicitly confirmed was 
their largest source of localization error. *Our own inference, not directly stated 
by the paper:* this method likely requires knowing the target's true size in 
advance, and could be thrown off if the target banks or changes its visible 
silhouette — worth testing ourselves before relying on it. Gives solid evidence for 
prioritizing a single camera for range, but flags a real need to test this 
bounding-box depth math against alternatives (optical flow, bearing-only tracking).

**Barišić, Petric, Bogdan (2022) — Brain over Brawn**
Proves a slower interceptor can catch a faster target using predictive math instead 
of tail-chasing — but the specific software approach is a dead end for our real-world 
scenario. Used a ZED Mini stereo camera for precise 3D coordinates and a 
pattern-matching algorithm to fit the target's flight path to a Bernoulli lemniscate 
(figure-eight shape), then flew to "cut the corner" and meet the target rather than 
chase it. Achieved 9/12 successful real-world intercepts, catching a target 30% 
faster than the interceptor itself in most simulated encounters. The fatal flaw: 
this only works because the target flies a known, repeating pattern. A real evasive 
drone flying erratically or in a straight line would break this method entirely. 
Good evidence that predictive guidance can beat faster targets — not usable as a 
direct software approach for us.

**Vrba, Heřt, Saska (2019) — Onboard Marker-Less Detection and Localization of 
Non-Cooperating Drones for Their Safe Interception**
The earlier, foundational paper behind the 2020 Vrba & Saska work above. Uses a 
depth camera (not plain RGB) to find isolated clusters of points floating in open 
space — a small flying drone shows up as a cluster disconnected from ground/large 
surfaces — giving full 3D position directly from the sensor, with no box-size 
guesswork needed. "Marker-less, non-cooperating" means the target needs no tag or 
special marking, which matches our real threat scenario. Tested in real flight, 
framed explicitly as the first stage of an interception system. Inherits the 
standard depth-camera range ceiling (similar to the ~20m limitation found in the 
2020 comparison) — this paper is best read as evidence for the accurate-but-short-range 
side of the sensor tradeoff, not as a standalone candidate.

**Ning, Zhang, Lin, Zhao (2024) — A Real-to-Sim-to-Real Approach for Vision-Based 
Autonomous MAV-Catching-MAV**
Solves a different problem than every other detection paper: not "which AI model 
is best," but "how do we get enough training data without flying thousands of real 
test flights." They collected ~500 real photos of an actual outdoor location, built 
a simulated 3D environment directly from those photos (rather than a generic fake 
environment), trained their detector entirely inside that simulation, then deployed 
it on a real camera for outdoor flight tests against a real target — narrowing the 
usual "sim-to-real gap" by grounding the simulator in real imagery from the start. 
Hardware: a DJI platform with RTK GPS and a gimballed camera with a laser 
rangefinder that gives accurate distance only when the target is centered in frame. 
No onboard compute or FPS figures were confirmed, so real-time edge-deployability 
is unproven here — this paper's value is the data-generation strategy, not a 
detection architecture to copy directly.

**Zhang et al. (2026) — Observability-Enhanced Target Motion Estimation via 
Bearing-Box: Theory and MAV Applications**
Currently the strongest candidate for solving bearing-only distance estimation on 
a monocular camera. Instead of using only the bounding box's center point (bearing 
angle), it uses the box's full changing width and height over time to mathematically 
separate the target's distance from its physical size — without needing to know 
either one in advance, fixing the exact flaw found in Vrba & Saska (2020). It also 
exploits a multirotor-specific physical fact — that a drone can only accelerate by 
tilting its body — to avoid relying on the fragile "assume constant velocity/ 
acceleration" motion models used elsewhere. Publicly released code and video. 
*Caveat: venue is unconfirmed (cited as IEEE T-RO by one source only), and 
real-world validation details ("extensive experimental validation" per abstract) 
have not been independently verified — this needs a direct read of the full paper 
before being treated as settled.*

**Jana, Tony, Bhise, Varun V.P., Ghose (2022) — Interception of an Aerial 
Manoeuvring Target Using Monocular Vision**
The cleanest example of guidance that uses zero distance estimation at all — pure 
pursuit, steering to keep the target's pixel centered in the camera frame, with 
distance never calculated anywhere in the loop. Tested first in ROS-Gazebo 
simulation, then in real outdoor field experiments. Successfully keeps the target 
in frame and achieves low miss distance, but only against low-speed targets — the 
authors explicitly list high-speed extension as unfinished future work. This 
approach also fundamentally requires the interceptor to be faster than the target, 
since it never predicts ahead, only reacts to the current position — a real 
evasive target moving faster than the interceptor cannot be caught by this method, 
no workaround exists within it.

**Yang, Bai, Quan (2025) — Line-of-Sight-Constrained Multicopter Interceptability**
A different kind of paper entirely — not a chase method, but a feasibility 
calculator. Computes a "reachable set" for both interceptor and target based on 
speed and turning ability, checks whether they can overlap, and explicitly factors 
in the interceptor's limited camera field of view (target must stay visible 
throughout, not just be physically reachable). Produces a single "interceptability 
index" — positive means catching the target is mathematically achievable if both 
sides play optimally; not-positive means it's impossible with that hardware, 
regardless of guidance algorithm. Theory and simulation only, no real hardware. 
Best used as a planning tool to sanity-check our hardware specs (top speed, camera 
FOV, turning rate) before committing to building/testing a prototype.

**(2026) — Autonomous Drone-on-Drone Interception Using an Integrated LiDAR–Vision 
Detection System for High-Precision Capture** *(Drones journal, MDPI — note weaker 
peer-review standards than our IEEE/AIAA papers; read claims with extra scrutiny)*
Combines LiDAR (long-range coarse detection via DBSCAN clustering + Moving Horizon 
Estimation tracking) with a global-shutter camera running custom-trained YOLO for 
close-range confirmation and refinement — a two-stage handoff rather than choosing 
one sensor. Runs on a Jetson Orin Nano, UART link to the flight controller at 500 
Hz. Reported detection accuracy under 0.4m at ranges beyond 40m (verified against 
RTK-GPS ground truth), detection range to 60m, over 90% vision-confirmation rate, 
multiple real autonomous interception missions. Confirmed to use Proportional 
Navigation guidance and net-based capture — but whether the PN is a plain or 
modified (re-engagement-capable, like Pliska's fix) version is not confirmed. 
Requires BOTH LiDAR and camera onboard, which doesn't resolve our SWaP-C constraint 
so much as relocate it — but the long-range-to-short-range sensor handoff pattern, 
and the global-shutter choice to avoid motion distortion at high closing speed, 
are both worth carrying forward regardless of final sensor choice.

**García, Caballero, González, Viguria, Ollero (2020) — Autonomous Drone with 
Ability to Track and Capture an Aerial Target** *(corrected entry)*
A MBZIRC 2020 Challenge 1 entry — a dedicated "capture aerial robot" built to 
intercept a target flying on a variable trajectory and speed (part of the standard 
MBZIRC figure-eight-style scripted task), plus burst randomly-placed balloons as a 
secondary task. Full GNC (Guidance, Navigation, Control) stack, tested in both 
simulation and real experiments per the abstract. *The exact capture mechanism 
(net, manipulator, or otherwise) is not confirmed — the paper is paywalled and 
only the abstract was retrieved.* Shares the same scripted-pattern fatal flaw as 
other MBZIRC entries in our pile (IISc's manipulator paper, NimbRo's 
balloon-popping paper) — useful mainly as a fourth team's engineering comparison 
point on the same competition task, not as a distinct capture-mechanism category.

**Rothe, Strohmeier, Montenegro (2019) — A Concept for Catching Drones with a Net 
Carried by Cooperative UAVs** *(paywalled — secondhand information only)*
The origin paper of the MIDRAS project line. Two or more drones fly in coordinated 
formation, physically carrying a net stretched between them — the formation itself 
becomes the net's frame, rather than one drone maneuvering a hanging net. Later 
papers cite this as establishing the foundational net-formation concept. *Could 
not confirm number of drones, sensor setup, or real flight vs. simulation status 
— full paper was inaccessible.*

**Rothe, Strohmeier, Montenegro (2025) — Autonomous Multi-UAV Net Defense System 
for Aerial Drone Interception** *(paywalled — abstract retrieved only)*
The engineering maturation of the 2019 concept. Combines centralized path planning 
with decentralized formation control, using a hybrid Leader-Follower + Virtual 
Structure control scheme to keep the formation coordinated. Adds a modified Model 
Reference Adaptive Controller (MRAC) to compensate for the net's unpredictable drag 
and weight effects on flight, plus a physical mechanical dampening system with 
integrated load sensing at the net attachment point to absorb capture impact and 
measure force in real time. *Real flight vs. simulation, number of drones tested, 
target type, and success rate were not confirmed — full paper inaccessible.* Most 
useful for the physical/mechanical dampening and load-sensing design, applicable 
even to a single-drone net system if we pursue that route.

---

## Gaps & Synthesis

**No paper solves the full pipeline the way we'd need it.** Every full end-to-end, 
real-flight-tested system in our pile (Pliska 2024, Brain over Brawn 2022) is built 
around either LiDAR or stereo — not the monocular-only setup we're most seriously 
considering for range/weight reasons. No paper combines monocular camera + proven 
guidance law + real flight + a genuinely evasive (non-scripted) target, all at once.

**The single biggest unresolved question:** none of the proven guidance laws (PN, 
MPC, pure pursuit) have been tested with bearing-only, camera-derived position 
estimates instead of LiDAR/depth data. Every guidance paper that performs well 
(Pliska, the LiDAR-Vision hybrid) was fed high-quality 3D position data. Every 
paper that uses only a camera (Jana et al., Yang & Quan) either skips distance 
estimation entirely (pure reactive) or hasn't been combined with a sophisticated 
predictive guidance law. This combination appears to be a genuine gap in the 
published literature, not something we've simply failed to find.

**The MBZIRC competition family (García 2020, and the IISc/NimbRo papers noted 
elsewhere) all share one fatal flaw:** the target flies a known, scripted pattern. 
Every guidance/prediction trick built on this assumption (including Brain over 
Brawn's figure-eight fit) is disqualified against a real, evasive target.

**Capture mechanism is the least-explored category overall.** Only two papers have 
confirmed real-flight net capture against a maneuvering target (Pliska 2024, and 
the 2026 LiDAR-Vision hybrid). Formation-carried nets (MIDRAS) and manipulator/grab 
mechanisms (MBZIRC family) exist but are either unconfirmed in detail (paywalled) 
or built for scripted targets. Kinetic/destructive capture is essentially absent 
from peer-reviewed literature — no vision-only system has demonstrated the 
single-digit-centimeter terminal accuracy this would require.