---
layout: page
title: Finite-State Visual Servo Control for Autonomous Target Landing
description: GPS-free target landing for a 189 g unpowered fixed-wing aircraft using only a low-cost IMU and monocular camera.
img: assets/img/projects/fixed-wing-landing/system-overview.jpg
importance: 3
category: research
---

How accurately can a lightweight fixed-wing aircraft land on a designated target when it cannot hover, has no propulsion after launch, and carries only low-cost sensing? This project develops a complete solution—from aerodynamic modeling and controller design to custom hardware and repeated flight tests—for autonomous target landing without GPS, airspeed sensing, or high-grade inertial navigation.

> **Paper scope.** This page covers the finite-state target-landing manuscript under review at _Aerospace Science and Technology_, including the custom platform and physical landing experiments. The earlier simulation-focused [CCC 2025 attitude-control paper]({{ '/projects/attitude-control/' | relative_url }}) is a separate publication.

{% include figure.liquid loading="eager" path="assets/img/projects/fixed-wing-landing/system-overview.jpg" title="Two-phase autonomous landing system: IMU-guided ascent followed by camera-guided descent" class="img-fluid rounded z-depth-1" %}

<div class="row row-cols-2 row-cols-md-4 g-3 text-center my-4">
  <div class="col"><div class="p-3 border rounded"><strong>189 g</strong><br><small>custom aircraft</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>¥534</strong><br><small>total hardware cost</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>20 / 20</strong><br><small>successful VSC landings</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>99.5%</strong><br><small>lower final radial error</small></div></div>
</div>

## Why the problem is difficult

Unlike a multirotor, an underactuated fixed-wing aircraft cannot stop or correct its position independently along each axis. The vehicle is launched at high speed, becomes fully unpowered after leaving the catapult, and must continuously trade altitude for forward motion. Its control surfaces are strongly coupled to airspeed and aerodynamic forces, while the low-cost camera and IMU introduce noise, delay, and limited field of view.

The key idea is to divide the landing into two regimes that match the information available during flight:

1. **IMU-guided ascent:** stabilize pitch and yaw immediately after launch, when the target may not yet be reliably visible.
2. **Monocular vision-guided descent:** once the aircraft passes the trajectory apex, use image-plane target error to command the rudder and elevator and drive the target toward the camera center.

## Finite-state visual-servo controller

The finite-state machine uses the measured pitch sign to switch between attitude stabilization and position regulation. During ascent, a lightweight pose controller protects the trajectory from launch disturbances. During descent, the camera extracts the target center and the visual-servo controller converts the normalized image-plane displacement into discrete control-surface commands. This architecture avoids dependence on absolute position, calibrated range, GPS, or an airspeed sensor.

The controller runs entirely onboard: IMU feedback is processed at **250 Hz**, while the servos accept commands at **333 Hz**. The same compact OpenMV-based sensing and computing unit handles the camera, inertial measurements, state transition, and actuator commands.

<div class="col-md-7 mx-auto">
{% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/finite-state-controller.jpg" title="Finite-state logic for switching from IMU pose control to monocular visual-servo position control" class="img-fluid rounded z-depth-1" %}
</div>

## Custom low-cost hardware

I designed and built the complete experimental hardware stack, including the **260 × 145 × 145 mm, 189 g aircraft**, movable rudder and elevator, onboard camera/IMU electronics, power distribution, protective housing, and an adjustable catapult launcher. The camera provides **640 × 480** imagery with a **95–120° field of view**. The platform reaches up to **15 m/s** and supports flight distances of approximately **5–26 m**, while the complete bill of materials is only **CNY 534**.

<div class="row align-items-center">
  <div class="col-md-7">
    {% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/aircraft-electronics.jpg" title="Custom aircraft, forward-facing camera, and compact onboard electronics" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-md-5">
    {% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/catapult-launcher.jpg" title="Adjustable launcher used to produce repeatable high-speed initial conditions" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

## From aerodynamic modeling to robust simulation

Because control-surface authority varies with flight condition, aerodynamic behavior was first characterized through CFD. The resulting lift, drag, and lateral-force trends were incorporated into a six-degree-of-freedom simulation so that controller development reflected the behavior of the physical aircraft rather than an idealized kinematic model.

{% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/cfd-flow-development.jpg" title="CFD snapshots showing airflow development around the aircraft" class="img-fluid rounded z-depth-1" %}

The simulation pipeline randomizes launch position, attitude, and velocity and explicitly models airflow disturbances, sensing noise, hardware delay, and aerodynamic uncertainty. This makes the evaluation a robustness test across realistic initial conditions rather than a single nominal trajectory.

{% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/simulation-pipeline.jpg" title="CFD-informed six-degree-of-freedom simulation and controller evaluation pipeline" class="img-fluid rounded z-depth-1" %}

### Simulation results

Across randomized trials, the proposed visual-servo controller (VSC) substantially outperformed the sliding-mode-control (SMC) baseline:

| Metric                               |    SMC baseline |        Proposed VSC |               Improvement |
| ------------------------------------ | --------------: | ------------------: | ------------------------: |
| Final normalized radial error        | 0.5049 ± 0.0059 | **0.0027 ± 0.0015** |           **99.5% lower** |
| RMS normalized radial error          | 1.4526 ± 0.0617 | **0.1732 ± 0.0159** |           **88.1% lower** |
| Settles inside the 0.3 target region |              No |             **Yes** | Stable target convergence |

The trajectory plots below show the practical difference: the baseline approaches the image center inconsistently, whereas VSC concentrates the terminal trajectories tightly around the target.

{% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/landing-outcomes.jpg" title="Image-plane trajectories over 20 trials: sliding-mode baseline (left) and proposed visual-servo control (right)" class="img-fluid rounded z-depth-1" %}

## Physical flight validation

The aircraft was launched at approximately **10–15 m/s** and then completed the entire trajectory without propulsion. Real flight tests covered sensing, state switching, control-surface actuation, target acquisition, and impact at the landing zone as one closed-loop system.

{% include figure.liquid loading="lazy" path="assets/img/projects/fixed-wing-landing/flight-sequence.jpg" title="Representative closed-loop descent and target landing sequence" class="img-fluid rounded z-depth-1" %}

In **20 physical trials per controller**, the SMC baseline achieved **15 successful landings (75%)**, while the proposed finite-state VSC achieved **20 successful landings (100%)**. The experiments demonstrate that accurate target landing is possible on an extremely low-cost, resource-constrained, underactuated aircraft without external localization infrastructure.

## My contributions

- Designed and assembled the aircraft, onboard electronics, control surfaces, and adjustable catapult test platform.
- Developed the finite-state IMU/vision control architecture and the onboard control implementation.
- Built the CFD-informed simulation and robustness evaluation workflow.
- Conducted repeated closed-loop flight experiments and analyzed the simulation and physical-test results.

This manuscript, **“Finite-State Visual Servo Control for Autonomous Target Landing of Low-Cost Underactuated Fixed-Wing Aircraft,”** is under review at _Aerospace Science and Technology_.
