---
layout: page
title: Point Cloud Reconstruction for Autonomous Earthworks
description: Completion of partial LiDAR scans from in-the-wild terrains
img: assets/img/projects/3_intro.png
importance: 1
category: all
related_publications: true
github: https://github.com/kirilllzaitsev/terrain_reconstruction
---

Inspired by {% cite hoeller2022neural %}, we build a framework to complete partial LiDAR scans for autonomous earthwork missions.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        {% include video.liquid path="assets/img/projects/bremgarten_raw.mkv" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=true %}
    </div>
    <div class="col-sm mt-2 mt-md-0">
        {% include video.liquid path="assets/img/projects/bremgarten_pred.mkv" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=true %}
    </div>
</div>

<div class="caption">
    Partial (left) vs completed (right) point clouds.
</div>

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        {% include video.liquid path="assets/img/projects/hong2_raw.mkv" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=true %}
    </div>
    <div class="col-sm mt-2 mt-md-0">
        {% include video.liquid path="assets/img/projects/hong2_pred.mkv" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=true %}
    </div>
</div>

<div class="caption">
    Partial (left) vs completed (right) point clouds.
</div>

A sample from the generated synthetic dataset:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3_intro.png" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=false %}
    </div>
</div>