---
layout: page
title: Expanding Grasp Reachability with Two-Stage Planning
description: Field-based pose-constrained planning that sweeps objects into high-manipulability regions before grasping.
img: assets/img/projects/grasp-planning/direct-vs-two-stage.jpg
importance: 2
category: research
---

An object may be positionally reachable yet impossible to grasp with the orientation required by the task. Near a manipulator's workspace boundary, top-down or otherwise pose-constrained grasps can force the arm toward singular, low-manipulability configurations. This project expands the effective graspable workspace by allowing the robot to **relocate first and grasp second**.

{% include figure.liquid loading="eager" path="assets/img/projects/grasp-planning/direct-vs-two-stage.jpg" title="Direct picking fails near the workspace boundary; the two-stage strategy sweeps the object into a favorable region before grasping" class="img-fluid rounded z-depth-1" %}

<div class="row row-cols-2 row-cols-md-4 g-3 text-center my-4">
  <div class="col"><div class="p-3 border rounded"><strong>27 / 30</strong><br><small>boundary grasps in simulation</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>0 / 30</strong><br><small>direct-pick boundary baseline</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>+50%</strong><br><small>real-world grasp radius</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>7-DoF</strong><br><small>RM75 validation platform</small></div></div>
</div>

## The workspace-boundary gap

Conventional reachability maps answer whether the end effector can reach a Cartesian location, but task-oriented grasping imposes additional orientation constraints. A top-down grasp, for example, may be infeasible even when the object lies inside the nominal workspace. Standard inverse kinematics can also return a solution close to a singularity, leaving insufficient dexterity for reliable execution.

The proposed planner reasons about a **task-conditioned pose family** and decides between:

- **Direct pick:** grasp immediately when the object is reachable and its pose-aware manipulability exceeds a threshold.
- **Two-stage pick:** sweep the object across the support surface to a nearby high-manipulability region, reapply the strict pose constraint, and then grasp.

{% include figure.liquid loading="lazy" path="assets/img/projects/grasp-planning/framework.jpg" title="Field construction, direct-pick feasibility test, and sweep-then-pick planning pipeline" class="img-fluid rounded z-depth-1" %}

## Three task-conditioned fields

The planner constructs three complementary representations directly from the robot's kinematic model:

1. **Physical reachability field** \(R(\mathbf{x})\): the position-only workspace, approximated from sampled forward kinematics and an alpha shape.
2. **Pose-constrained reachability field** \(R\_{\mathcal{P}}(\mathbf{x})\): the subset reachable while satisfying the required grasp orientation.
3. **Pose-aware manipulability field** \(M(\mathbf{x})\): a continuous score based on the minimum eigenvalue of the translational Jacobian, spatially aggregated and weighted by orientation consistency.

Low values of \(M(\mathbf{x})\) identify positions that are nominally reachable but ill-conditioned under the task pose. The fields are built offline; online planning requires only lightweight queries and sweep-target selection.

{% include figure.liquid loading="lazy" path="assets/img/projects/grasp-planning/task-conditioned-fields.jpg" title="Physical reachability, pose-constrained reachability, and pose-aware manipulability fields for the RM75" class="img-fluid rounded z-depth-1" %}

## Human-inspired sweep-then-pick policy

When direct grasping is infeasible, the policy searches the object's support plane for a relocation target with high pose-aware manipulability. It first searches within a bounded sweep radius; if no valid point exists, it relaxes the distance constraint and selects the best reachable candidate. A Cartesian sweep moves the object at constant height, after which the original grasp pose constraint is restored for the final pickup.

This decomposition avoids forcing the manipulator to solve a difficult constrained grasp at the workspace edge. It uses a relaxed pose during relocation and reserves the strict top-down orientation for the final, better-conditioned grasp.

## Simulation results

The planner was evaluated on a 7-DoF RM75 at three workspace-boundary positions and two central positions, with 10 trials per location.

| Location | Initial manipulability | Direct pick | Two-stage pick | Manipulability after relocation |
| -------- | ---------------------: | ----------: | -------------: | ------------------------------: |
| P1       |                  0.146 |        0/10 |       **9/10** |                           0.951 |
| P2       |                  0.000 |        0/10 |      **10/10** |                           0.951 |
| P3       |                  0.000 |        0/10 |       **8/10** |                           0.558 |
| G1-G2    |            0.517-0.669 |       20/20 |      **20/20** |          No relocation required |

The direct baseline failed in all **30 boundary trials**, while the proposed strategy succeeded in **27/30**. At the central locations, both methods achieved 20/20 success, confirming that the planner does not introduce unnecessary sweeping when direct grasping is already well conditioned.

{% include figure.liquid loading="lazy" path="assets/img/projects/grasp-planning/simulation-sequences.jpg" title="Simulation sequences for a direct central grasp and a boundary-case sweep-then-pick recovery" class="img-fluid rounded z-depth-1" %}

## Real-world validation

The full pipeline transferred to the physical RM75 without modification and was tested with a tape roll and a 3D-printed industrial part. Direct picking handled central high-manipulability cases, while the two-stage policy recovered boundary cases by relocating the object before pickup.

{% include figure.liquid loading="lazy" path="assets/img/projects/grasp-planning/hardware-sequences.jpg" title="Physical RM75 experiments with two target objects at central and boundary workspace locations" class="img-fluid rounded z-depth-1" %}

The effective pose-constrained grasp radius increased from **0.38 m to 0.57 m**, a **50% expansion**. Among four low-manipulability test positions, the two-stage method recovered three; the remaining case lay outside the feasible sweep range, making the limitation explicit rather than producing an unstable grasp attempt.

## My contributions

- Developed the physical reachability, pose-constrained reachability, and pose-aware manipulability field representations.
- Designed the policy that selects direct picking or human-inspired object relocation based on task-conditioned graspability.
- Implemented the simulation and real-robot RM75 planning pipeline and evaluated workspace-boundary cases.
- Analyzed success, manipulability improvement, unnecessary-action avoidance, and sim-to-real transfer.

The full paper, **“Expanding Grasp Reachability: Field-based Pose-constrained Two-stage Grasp Planning,”** was accepted for an oral presentation at **ICoSR 2026**.
