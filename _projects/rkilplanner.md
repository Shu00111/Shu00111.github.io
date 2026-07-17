---
layout: page
title: RKILPlanner for Human-Like Highway Driving
description: Risk-aware kinematic imitation learning with Driver's Risk Fields and explicit driver-style encoding.
img: assets/img/projects/rkilplanner/human-like-trajectory.jpg
importance: 4
category: research
---

Safe highway driving is not only a matter of avoiding collisions. Human drivers also express consistent preferences through acceleration, braking, lane position, and subtle lateral corrections. **RKILPlanner** combines explicit spatial risk perception, individual driving style, and vehicle kinematics to predict physically consistent, human-like trajectories over a safety-critical **2.5 s horizon**.

{% include figure.liquid loading="eager" path="assets/img/projects/rkilplanner/human-like-trajectory.jpg" title="RKILPlanner follows the naturalistic human trajectory while a history-only LSTM deviates laterally" class="img-fluid rounded z-depth-1" %}

<div class="row row-cols-2 row-cols-md-4 g-3 text-center my-4">
  <div class="col"><div class="p-3 border rounded"><strong>28,641</strong><br><small>HighD test samples</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>0.159 m</strong><br><small>average displacement error</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>0.443 m</strong><br><small>final displacement error</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>0.3%</strong><br><small>miss rate</small></div></div>
</div>

## Risk-aware and style-aware prediction

Purely kinematic baselines cannot reproduce acceleration and deceleration decisions, while unconstrained neural predictors may generate trajectories that are accurate at short horizons but physically implausible or unstable later. RKILPlanner introduces three complementary inputs:

1. **One-second trajectory history:** 25 frames of relative position and longitudinal/lateral velocity, encoded by an LSTM.
2. **Local Driver's Risk Field:** a normalized \(41 \times 60\) risk map centered on the ego vehicle, encoded by a CNN.
3. **Five-dimensional driver-style vector:** mean speed, longitudinal-speed variation, lateral variation, peak acceleration, and normalized lane position.

The fused representation is decoded into acceleration and yaw-rate commands—not Cartesian points—and passed through a differentiable kinematic integration layer. This enforces continuous velocity and heading evolution and allows physical structure to participate directly in end-to-end imitation learning.

{% include figure.liquid loading="lazy" path="assets/img/projects/rkilplanner/architecture.jpg" title="RKILPlanner architecture: temporal history, spatial risk, and driver style fused before differentiable kinematic integration" class="img-fluid rounded z-depth-1" %}

## Driver's Risk Field

The Driver's Risk Field (DRF) represents perceived road risk as a continuous spatial distribution rather than a single scalar or hard safety boundary. Risk density follows the anticipated motion of surrounding vehicles and expands laterally with distance and steering behavior. RKILPlanner crops this global field around the ego vehicle so the CNN receives a compact, interpretable map of the low- and high-risk corridors that matter for the next 2.5 seconds.

This risk representation contributes most when the prediction horizon becomes long enough for surrounding traffic to influence driver decisions. At short horizons, motion is dominated by current kinematics; beyond one second, risk-aware lateral reasoning becomes increasingly important.

## HighD evaluation

The model was trained end-to-end on the naturalistic **HighD** highway dataset, using 1.0 s of history to predict 62 future frames at 25 Hz (2.48 s, approximately 2.5 s). It was compared with a constant-velocity baseline, a direct-regression VanillaLSTM, and an ablation that removes the DRF risk branch.

| Metric    | RKILPlanner | No-risk ablation | Constant velocity | VanillaLSTM |
| --------- | ----------: | ---------------: | ----------------: | ----------: |
| ADE       | **0.159 m** |          0.168 m |           0.208 m |     0.464 m |
| FDE       | **0.443 m** |          0.467 m |           0.576 m |     0.744 m |
| RMSE      | **0.208 m** |          0.220 m |           0.272 m |     0.501 m |
| Miss rate |   **0.003** |            0.014 |             0.028 |       0.042 |

Relative to constant velocity, RKILPlanner reduced ADE by **23.7%** and FDE by **23.1%**. Relative to VanillaLSTM, it reduced ADE by **65.8%**, FDE by **40.4%**, and the miss rate from **4.2% to 0.3%**.

{% include figure.liquid loading="lazy" path="assets/img/projects/rkilplanner/main-metrics.jpg" title="ADE, FDE, RMSE, and miss-rate comparison on 28,641 HighD samples" class="img-fluid rounded z-depth-1" %}

## What the risk branch contributes

The no-risk ablation is close to the complete model over the first 0.6 s, but the gap grows monotonically with prediction time and reaches **6.0% at 2.5 s**. The full model also reduces lateral ADE by **11.5%** relative to the ablation, compared with a 2.7% longitudinal improvement. This asymmetric gain matches the purpose of the DRF branch: capturing lane-level spatial risk and proactive lateral adjustment.

{% include figure.liquid loading="lazy" path="assets/img/projects/rkilplanner/horizon-analysis.jpg" title="Segmented error over the prediction horizon and longitudinal/lateral ADE decomposition" class="img-fluid rounded z-depth-1" %}

The kinematic decoder predicts a mean trajectory length of **71.15 m**, only **0.17%** from the 71.27 m ground-truth mean. It therefore reproduces both the local path shape and the physically meaningful travel distance without post-processing.

## Qualitative risk-field behavior

In dense traffic, the risk field changes as surrounding vehicles move. RKILPlanner's predicted trajectory follows the evolving low-risk corridor and remains close to the recorded human trajectory during deceleration and subtle lane-position adjustment.

{% include figure.liquid loading="lazy" path="assets/img/projects/rkilplanner/risk-field-sequence.jpg" title="Method comparison and four-frame DRF sequence showing the prediction following an evolving low-risk corridor" class="img-fluid rounded z-depth-1" %}

<video class="img-fluid rounded z-depth-1" controls preload="metadata" poster="{{ '/assets/img/projects/rkilplanner/human-like-trajectory.jpg' | relative_url }}">
  <source src="{{ '/assets/video/rkilplanner-demo.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support embedded video.
</video>

<div class="caption">Highway trajectory prediction and dynamic Driver's Risk Field visualization.</div>

## My contributions

- Developed the unified DRF, driver-style, and kinematic imitation-learning formulation.
- Built the LSTM/CNN/style three-branch network and differentiable acceleration-yaw-rate integration decoder.
- Constructed the HighD processing and evaluation pipeline across 28,641 samples.
- Conducted baseline comparisons, risk-branch ablations, horizon analysis, and qualitative risk-field visualization.

The manuscript, **“RKILPlanner: Risk-based Kinematic Imitation Learning Planning Towards Human-Like Driving in Highway Scenarios,”** has been submitted to **ROBIO 2026**.
