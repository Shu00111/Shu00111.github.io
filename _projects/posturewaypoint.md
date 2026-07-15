---
layout: page
title: PostureWaypoint for Reactive Whole-Arm Manipulation
description: Configuration-space waypoint recovery for online manipulation in constrained environments.
img: assets/img/publication_preview/posturewaypoint.jpg
importance: 1
category: research
---

PostureWaypoint recovers a feasible whole-arm configuration when a reactive controller stalls or an end-effector waypoint leaves the rest of the arm in collision. The method combines geometric anchor generation, constrained posture synthesis, and reactive execution while preserving end-effector intent.

{% include figure.liquid loading="eager" path="assets/img/publication_preview/posturewaypoint.jpg" title="PostureWaypoint method comparison" class="img-fluid rounded z-depth-1" %}

The evaluation covers RM75, Franka Panda, UR5e, and KUKA iiwa manipulators across constrained simulation tasks, followed by real-robot validation on the RM75 platform.

{% include figure.liquid path="assets/img/publication_preview/posturewaypoint-real.jpg" title="PostureWaypoint real-robot experiments" class="img-fluid rounded z-depth-1" %}

<video class="img-fluid rounded z-depth-1" controls preload="metadata" poster="{{ '/assets/img/publication_preview/posturewaypoint-real.jpg' | relative_url }}">
  <source src="{{ '/assets/video/posturewaypoint-demo.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support embedded video.
</video>

<div class="caption">Compressed web preview of the comparative real-robot experiments.</div>
