---
layout: page
title: Multi-Object 6D Pose Tracking With Confidence Estimation
description: tracking of 6D pose of multiple objects with confidence estimation
img: assets/img/projects/1_intro.png
importance: 1
category: all
related_publications: true
---

Inspired by {% cite wen2024foundationpose %}, we build a framework to track 6D poses of multiple objects with pose confidence estimation.

**Method**:

1. Generate synthetic dataset in IsaacSim
2. Train a model based on the DETR transformer extended to time domain
3. Design a method for pose confidence estimation
4. Test the model in robotic manipulation scenarios

**Result**: successful application to in-hand manipulation

**Technologies**: Python, PyTorch, IsaacSim, ROS

**Topics**: simulation, multi-object 6D pose tracking, transformer, DETR

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/img/projects/1_manip.mp4" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=false %}
    </div>
</div>
<div class="caption">
    CAD-based FoundationPose (green) vs Ours (yellow). In a zero-shot setup, the model trained on 40k synthetic images transfers sufficiently well to the real-world.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/img/projects/1_cubes.mp4" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=false %}
    </div>
</div>
<div class="caption">
    Groundtruth (green) vs Ours (yellow). A video from the test set of a dataset with multiple identical cubes per frame.
</div>



