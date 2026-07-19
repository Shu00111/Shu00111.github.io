---
layout: page
title: PostureWaypoint for Reactive Whole-Arm Manipulation
description: Configuration-space waypoint recovery that resolves whole-arm posture ambiguity in constrained environments.
img: assets/img/projects/posturewaypoint/real-robot-comparison.jpg
importance: 1
category: research
---

Reactive controllers can respond quickly to changing environments, but their local target-attraction and obstacle-avoidance objectives may conflict. The robot can stall, oscillate, or move its end effector around an obstacle while the elbow, forearm, or wrist remains in collision. **PostureWaypoint** addresses the missing question in conventional waypoint recovery: not only _where should the end effector go next_, but _what should the entire arm configuration be_?

{% include figure.liquid loading="eager" path="assets/img/projects/posturewaypoint/real-robot-comparison.jpg" title="Task-space recovery can leave the arm stalled or in collision; PostureWaypoint selects an explicit collision-free whole-arm configuration" class="img-fluid rounded z-depth-1" %}

<div class="row row-cols-2 row-cols-md-4 g-3 text-center my-4">
  <div class="col"><div class="p-3 border rounded"><strong>4 robots</strong><br><small>different kinematic structures</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>1,200</strong><br><small>episodes per method</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>70.2%</strong><br><small>mean whole-arm success</small></div></div>
  <div class="col"><div class="p-3 border rounded"><strong>2.73 ms</strong><br><small>mean control time</small></div></div>
</div>

## From Cartesian waypoints to whole-arm postures

A Cartesian bypass waypoint defines an end-effector location, but a redundant manipulator may realize that point with many joint configurations. Those configurations can differ sharply in link clearance, distance to joint limits, orientation consistency, and required posture change. A successful hand trajectory therefore does not guarantee a safe whole-arm trajectory.

PostureWaypoint lifts a locally generated task-space anchor into a **configuration-space waypoint** that explicitly encodes a feasible articulated posture. It preserves the modularity and high-rate execution of reactive control without constructing a complete global path or requiring task-specific training.

{% include figure.liquid loading="lazy" path="assets/img/projects/posturewaypoint/framework.jpg" title="Event-triggered PostureWaypoint framework: detect blockage, generate a bypass region, synthesize a configuration-space waypoint, and resume reactive execution" class="img-fluid rounded z-depth-1" %}

## Recovery pipeline

1. **Event-triggered mode switching:** monitor task progress and whole-arm clearance; activate recovery only when the nominal reactive controller becomes blocked or unsafe.
2. **Geometric-SDF anchor generation:** use local obstacle geometry and signed-distance information to identify a compact bypass region in task space.
3. **Constrained posture synthesis:** solve for a whole-arm waypoint using multi-seed initialization and sequential quadratic programming (SQP), jointly enforcing kinematics, link-level clearance, orientation, joint limits, and minimal posture deviation.
4. **Posture-guided execution:** inject the synthesized configuration as a temporary reactive objective and use a progress-preserving control barrier function (CBF) filter to correct unsafe or ineffective commands.

The SQP refiner repeatedly convexifies the local nonlinear configuration-space problem. Each optimization point represents the complete articulated robot, so collision avoidance is enforced at the posture level rather than only at the end effector.

{% include figure.liquid loading="lazy" path="assets/img/projects/posturewaypoint/final-SQP.png" title="Local SQP refinement converts a colliding configuration into a feasible whole-arm recovery posture" class="img-fluid rounded z-depth-1 w-75 d-block mx-auto" %}

## Cross-robot evaluation

The method was evaluated in MuJoCo at **50 Hz** on four manipulators: RM75-B, Franka Panda, UR5e, and KUKA iiwa. Six three-dimensional tasks cover free reaching, side obstacles, narrow slits, pose-constrained blocks, noisy obstacle perception, and dynamically drifting obstacles.

{% include figure.liquid loading="lazy" path="assets/img/projects/posturewaypoint/task-suite.jpg" title="Six evaluation tasks spanning free motion, constrained geometry, sensing uncertainty, and obstacle motion" class="img-fluid rounded z-depth-1" %}

For each of the 24 robot-task pairs, 50 randomized episodes were generated, yielding **1,200 matched episodes per method**. PostureWaypoint was compared with RMPflow, multipoint RMP avoidance, CBF-RMPflow, Progress-CBF, and RRT-Connect.

| Metric                                       | Strongest comparison | PostureWaypoint |
| -------------------------------------------- | -------------------: | --------------: |
| Mean whole-arm success                       | 64.7% (Progress-CBF) |       **70.2%** |
| Target-reaching success                      |      94.7% (RMPflow) |       **96.4%** |
| Collision-free rate among online controllers | 67.5% (Progress-CBF) |       **71.8%** |
| Mean computation per control step            | 20 ms control budget |     **2.73 ms** |

The method achieved the best or tied-best result in **16 of 24 robot-task pairs**. Removing configuration-space synthesis reduced mean success from **70.2% to 52.7%**, showing that the dominant gain comes from selecting an explicit whole-arm posture rather than only a better Cartesian bypass point.

{% include figure.liquid loading="lazy" path="assets/img/projects/posturewaypoint/cross-robot-results.jpg" title="Strict whole-arm success across four robot morphologies and six constrained tasks" class="img-fluid rounded z-depth-1" %}

## Real-robot validation

Hardware experiments used an RM75 manipulator in two distinct constrained workspaces: Narrow Slit and Pose-Constrained Block. Each method was evaluated over 20 trials per task using the same reach, collision, and whole-arm success criteria as in simulation.

{% include figure.liquid loading="lazy" path="assets/img/projects/posturewaypoint/hardware-validation.jpg" title="RM75 hardware comparison: nominal reactive control, task-space recovery, and configuration-space PostureWaypoint" class="img-fluid rounded z-depth-1" %}

PostureWaypoint achieved **100% strict success (20/20)** in both tasks. In the pose-constrained block task, it also increased mean closest-approach clearance from **2.1 cm to 3.1 cm** relative to task-space recovery. In the narrow slit, it improved completion reliability from 75% for RMPflow and 95% for task-space recovery to **100%**.

<video class="img-fluid rounded z-depth-1" controls preload="metadata" poster="{{ '/assets/img/projects/posturewaypoint/hardware-validation.jpg' | relative_url }}">
  <source src="{{ '/assets/video/posturewaypoint-demo.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support embedded video.
</video>

<div class="caption">Comparative real-robot experiments in constrained three-dimensional workspaces.</div>

## My contributions

- Developed the configuration-space formulation that resolves the posture ambiguity of Cartesian waypoint recovery.
- Integrated geometric-SDF anchor generation, multi-seed SQP posture synthesis, reactive execution, and CBF filtering into one online framework.
- Built the four-robot, six-task randomized evaluation and conducted RM75 hardware experiments.
- Analyzed cross-robot generalization, ablations, real-time computation, and whole-arm safety metrics.

The manuscript, **“From End-Effector Targets to Whole-Arm Postures: Configuration-Space Waypoint Recovery for Reactive Manipulation,”** has been submitted to _IEEE Robotics and Automation Letters_.
