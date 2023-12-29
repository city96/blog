---
layout: post
title: "Anime Classifier - Chromatic aberration"
date: 2023-11-29 00:00:00 +0000
categories: release
---
{% assign media = "/" | append: page.path | replace: "_posts/","media/" | replace: ".md",""  %}

[Code](https://github.com/city96/CityClassifiers) \| [Live Demo](https://huggingface.co/spaces/city96/AnimeClassifiers-demo) \| [Model download](https://huggingface.co/city96/AnimeClassifiers)

*Excerpt from readme. Click any of the links above for more info.*

### Design goals

The goal was to detect [chromatic aberration](https://en.wikipedia.org/wiki/Chromatic_aberration?useskin=vector) in images.

For some odd reason, this effect has become a popular post processing effect to apply to images and drawings. While attempting to train an ESRGAN model, I noticed an odd halo around images and quickly figured out that this effect was the cause. This classifier aims to work as a base filter to remove such images from the dataset.

### Issues

- Seems to get confused by excessive HSV noise
- Triggers even if the effect is only applied to the background
- Sometimes triggers on rough linework/sketches (i.e. multiple semi-transparent lines overlapping)
- Low accuracy on 3D/2.5D with possible false positives.

### Training

The training settings can be found in the `config/CCAnime-ChromaticAberration-v1.yaml` file (1.5e-6 LR, cosine scheduler, 30K steps).

![loss]({{media}}/loss.png)

![loss-eval]({{media}}/loss-eval.png)


Final dataset score distribution for v1.16:
```
3215 images in dataset.
0_reg       -  395 ||||
0_reg_booru - 1805 ||||||||||||||||||||||
1_chroma    -  515 ||||||
1_synthetic -  500 ||||||

Class ratios:
00 - 2200 |||||||||||||||||||||||||||
01 - 1015 ||||||||||||
```

Version history:

- v1.0 - Initial test model, dataset is fully synthetic (500 images). Effect added by shifting red/blue channel by a random amount using chaiNNer.
- v1.1 - Added 300 images tagged "chromatic_aberration" from gelbooru. Added first 1000 images from danbooru2021 as reg images
- v1.2 - Used the newly trained predictor to filter the existing datasets - found ~70 positives in the reg set and ~30 false positives in the target set.
- v1.3-v1.16 - Repeatedly ran predictor against various datasets, adding false positives/negatives back into the dataset, sometimes running against the training set to filter out misclassified images as the predictor got better. Added/removed images were manually checked (My eyes hurt).
