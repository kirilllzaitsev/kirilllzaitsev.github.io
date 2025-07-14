---
layout: page
title: Monocular Depth Estimation With Geometrical Priors
description: Improve monocular depth estimation with geometrical cues
img: assets/img/projects/4_intro.jpg
importance: 1
category: all
related_publications: true
github: https://github.com/kirilllzaitsev/layout_aware_monodepth
---

Leveraging a state-of-the-art line detector {% cite pautrat2023deeplsdlinesegmentdetection %}, we build a framework for self-supervised monocular depth estimation with geometrical supervision based on line reprojection and vanishing points.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/4_intro.jpg" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=false %}
    </div>
</div>
<div class="caption">
    A sample from the KITTI-2015 dataset with detected lines.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/4_sample.jpg" class="img-fluid rounded z-depth-1 mx-auto d-block" controls=true autoplay=false %}
    </div>
</div>
<div class="caption">
    A sample from the KITTI-2015 test set comparing Ours vs Monodepth2 (vanilla). The network overestimates depth for distant parts of the scene while achieving better quality on objects outlined with lines (e.g., tree trunks, bridges)
</div>

