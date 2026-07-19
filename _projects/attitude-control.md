---
layout: page
title: Kalman Visual Servo-PID Attitude Control
description: Simulation-based pitch-yaw control for low-cost unpowered aircraft under noisy monocular measurements.
img: assets/img/projects/attitude-control/task-overview.jpg
importance: 5
category: research
---

This work studies how a small unpowered aircraft can regulate pitch and yaw using only monocular image-plane feedback under strong measurement noise. It introduces a **Kalman-filtered visual servo-PID controller** with adaptive gains, dynamically adjusted noise covariances, and loss-based parameter optimization.

<!-- > **Paper scope.** This page covers the simulation-focused paper published at **CCC 2025**. -->

{% include figure.liquid loading="eager" path="assets/img/projects/attitude-control/task-overview.jpg" title="Unpowered landing task and visual-servo objective: move the projected target to the image-plane origin" class="img-fluid rounded z-depth-1" %}

<div class="row row-cols-2 row-cols-md-4 g-3 text-center my-4">
  <div class="col"><div class="p-3 border rounded"><strong>1.3650</strong><br><small>lowest average error</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>48.4%</strong><br><small>lower error than SMC</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>41.1%</strong><br><small>lower error than VSC</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>σ = 0.5</strong><br><small>strong-noise evaluation</small></div></div>
</div>

## Control problem

The aircraft is launched at **10-15 m/s** and receives no further thrust after leaving the launcher. Its trajectory is adjusted only through the rudder and elevator, which change aerodynamic forces and therefore pitch and yaw acceleration. A monocular camera observes the landing target, and the controller minimizes the target's displacement from the image-plane origin.

This formulation avoids GPS, an IMU, and high-cost navigation sensors. The main challenge is that image noise, measurement error, nonlinear aircraft dynamics, environmental disturbance, and limited control-surface authority can destabilize direct visual servoing.

{% include figure.liquid loading="lazy" path="assets/img/projects/attitude-control/coordinate-model.jpg" title="Aircraft pitch-yaw definition, velocity coordinate frame, and unpowered flight trajectory" class="img-fluid rounded z-depth-1" %}

## Kalman visual servo-PID optimization

The controller combines four elements:

1. **Image-based visual servoing:** map the ground target into image coordinates \((u,v)\) and use the projection error as the control objective.
2. **Kalman filtering:** predict the target state and correct it with noisy camera measurements, estimating both image-plane position and velocity.
3. **Adaptive PID control:** vary \(K_p\), \(K_i\), and \(K_d\) with the current error so large deviations receive a strong response while small errors avoid excessive correction and jitter.
4. **Dynamic noise and loss optimization:** adjust process covariance \(Q\) and measurement covariance \(R\) with the observed error and optimize controller/filter parameters using a trajectory-estimation loss.

Together, these modules improve convergence speed, reduce steady-state error and overshoot, and make the controller less sensitive to noisy visual measurements.

## Baseline comparison

Simulations tested four initial image-plane directions and compared sliding-mode control (SMC), standard visual servo control (VSC), PID-enhanced VSC, adaptive PID-VSC, and the complete Kalman-filtered optimized controller.

{% include figure.liquid loading="lazy" path="assets/img/projects/attitude-control/controller-comparison.jpg" title="Image-plane trajectories and pitch-yaw convergence for the baseline and PID-enhanced controllers" class="img-fluid rounded z-depth-1" %}

Under Gaussian disturbance with \(\sigma=0.2\), the complete method achieved the lowest average trajectory error:

| Controller                                    | Average error |
| --------------------------------------------- | ------------: |
| Sliding-mode control                          |        2.6440 |
| Visual servo control                          |        2.3163 |
| Kalman-filtered visual servo control          |        1.5651 |
| Kalman-filtered PID visual servo control      |        1.4213 |
| **Optimized Kalman PID visual servo control** |    **1.3650** |

The optimized controller reduced average error by **48.4% relative to SMC** and **41.1% relative to standard VSC**.

## Robustness under stronger noise

The Kalman filter brings noisy observations back toward the underlying trajectory, while adaptive PID and dynamic covariance adjustment preserve smoother tracking when the disturbance increases.

{% include figure.liquid loading="lazy" path="assets/img/projects/attitude-control/noise-robustness.jpg" title="Noisy measurements, Kalman state estimates, and optimized tracking under nominal and stronger Gaussian disturbances" class="img-fluid rounded z-depth-1" %}

| Noise level | Kalman PID-VSC | Optimized Kalman PID-VSC |
| ----------- | -------------: | -----------------------: |
| \sigma=0.2  |         1.4213 |               **1.3650** |
| \sigma=0.5  |         2.7452 |               **2.7050** |

These results establish the paper's specific contribution: an algorithmic improvement in simulated low-cost attitude regulation under noisy vision. They do not represent the later paper's finite-state controller or physical target-landing trials.

## My contributions

- Developed the monocular image-plane attitude-control formulation for pitch-yaw trajectory correction.
- Integrated Kalman state estimation, adaptive PID gains, dynamic noise covariance adjustment, and loss-based parameter optimization.
- Built the noisy simulation study across multiple initial directions and controller baselines.
- Analyzed convergence, steady-state error, overshoot, and robustness under different Gaussian noise levels.

The paper, **“Attitude Control of Unpowered Aircraft in Flight Based on Kalman Filter Visual Servo-PID Optimization Algorithm,”** was published and presented orally at the **44th Chinese Control Conference (CCC 2025)**.
