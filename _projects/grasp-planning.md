---
layout: page
title: Field-based Pose-constrained Two-stage Grasp Planning
description: Extending grasp reachability near manipulator workspace boundaries.
img: assets/img/publication_preview/grasp-planning.jpg
importance: 2
category: research
---

This project constructs reachability and manipulability fields directly from the robot model to evaluate pose-constrained grasps. When a direct grasp is infeasible, a two-stage sweep-then-pick strategy relocates the object into a reachable region before grasping.

{% include figure.liquid loading="eager" path="assets/img/publication_preview/grasp-planning.jpg" title="Direct pick and two-stage grasp planning comparison" class="img-fluid rounded z-depth-1" %}

The method was validated in simulation and on a 7-DoF RM75 manipulator. The corresponding full paper was accepted for an oral presentation at ICoSR 2026.
