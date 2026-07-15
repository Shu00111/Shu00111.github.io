---
layout: page
title: RKILPlanner
description: Risk-based kinematic imitation learning for human-like highway driving.
img: assets/img/publication_preview/rkilplanner.jpg
importance: 4
category: research
---

RKILPlanner combines Driver's Risk Field maps, driver-style encoding, and LSTM/CNN features in a differentiable kinematic imitation-learning planner. The objective is to reproduce human-like driving preferences while retaining explicit kinematic structure.

{% include figure.liquid loading="eager" path="assets/img/publication_preview/rkilplanner.jpg" title="RKILPlanner highway trajectory comparison" class="img-fluid rounded z-depth-1" %}

The planner was evaluated on the HighD naturalistic highway dataset. The manuscript has been submitted to ROBIO 2026.

<video class="img-fluid rounded z-depth-1" controls preload="metadata" poster="{{ '/assets/img/publication_preview/rkilplanner.jpg' | relative_url }}">
  <source src="{{ '/assets/video/rkilplanner-demo.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support embedded video.
</video>

<div class="caption">Highway-scene trajectory and risk-field visualization.</div>
