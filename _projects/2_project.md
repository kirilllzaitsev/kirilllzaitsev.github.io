---
layout: page
title: Multi-task Learning in Urban Environments
description: jointly solving monocular depth estimation and semantic segmentation
img: assets/img/projects/2_intro.jpg
importance: 1
category: all
related_publications: true
---

The goal is to understand how several paradigms of multi-task learning work by implementing and analyzing their results.

Following the taxonomy of MTL models outlined in {% cite githubGitHubThumlawesomemultitasklearning %}, the following classes of architectures are implemented:

- Hard parameter sharing, a shared backbone with shallow task-specific heads.
- Soft parameter sharing, Cross-Stitch Networks, {% cite misra2016cross %}.
- Modulation & adapters, MTAN, {% cite liu2019end %}.

**Method**:

1. Implement from scratch models for task adaptation and hard/soft parameter sharing
2. Train and analyze performance of the models on KITTI, NYUv2, and CityScapes datasets

**Result**: benchmarking and analysis of several state-of-the-art methods in multi-task learning (available at [this link](https://github.com/kirilllzaitsev/vision_mtl?tab=readme-ov-file))

**Technologies**: Python, PyTorch

**Keywords**: multi-task learning, semantic segmentation, depth estimation

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/2_intro.jpg" class="img-fluid rounded z-depth-1"%}
    </div>
</div>
<div class="caption">
    Hard parameter sharing (HS) with weights pretrained on Imagenet (+P) and optimized weights for depth and segmentation losses (+_tuned_loss_weights), Soft parameter sharing (CSNet), Modulation & adapters (MTAN).
</div>
