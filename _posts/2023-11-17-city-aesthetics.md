---
layout: post
title: "City Aesthetics - Anime"
date: 2023-11-17 00:00:00 +0000
categories: release
---
{% assign media = "/" | append: page.path | replace: "_posts/","media/" | replace: ".md",""  %}

[Code](https://github.com/city96/CityClassifiers) \| [Live Demo](https://huggingface.co/spaces/city96/CityAesthetics-demo) \| [Model download](https://huggingface.co/city96/CityAesthetics)

### Design goals

The goal was to create an aesthetic predictor that can work well on one specific type of image (in this case, anime) while filtering out everything else. To achieve this, the model was trained on a set of 3080 hand-scored images with multiple refinement steps, where false positives and negatives would be added to the training set with corrected scores after each test run.

This model focuses on as few false positives as possible. Only having one type of media seems to help with this, as predictors that attempt to do both real life and 2D images tend to produce false positives. If one were to have a mixed dataset with both types of images, then the simplest solution would be to use two separate aesthetic score models and a classifier to pick the appropriate one to use.

#### Intentional biases

- Completely negative towards real life photos (ideal score of 0%)
- Strongly Negative towards text (subtitles, memes, etc) and manga panels
- Fairly negative towards 3D and to some extent 2.5D images
- Negative towards western cartoons and stylized images (chibi, parody)

#### Issues

- Tends to filter male characters due to being underrepresented in the training set
- Requires at least 1 subject to be present in the image - doesn't work for scenery/landscapes
- Noticeable positive bias towards anime characters with animal ears
- Hit-or-miss with AI generated images due to style/quality not being correlated

#### Out-of-scope

- This model is not meant for moderation/live filtering/etc
- The demo code is not meant to work with large-scale datasets and is therefore only single-threaded. If you're working on something that requires an optimized version that can work on pre-computed CLIP embeddings for faster iteration, feel free to [contact me](mailto:city@v100s.net).

### Usecases

The main usecase will be to provide baseline filtering on large datasets (i.e. a high pass filter). For this, the score brackets were decided as follows:

- <10% - Real life photos, noise, excessive text (subtitles, memes, etc)
- 10-20% - Manga panels, images with no subject, non-human subjects
- 20-40% - Sketches, oekaki, rough lineart (score depends on quality)
- 40-50% - Flat shading, TV anime screenshots, average images
- \>50% - "High quality" images based on my personal style preferences

The \>60% score range is intended to help pick out the "best" images from a dataset. One could use it to filter by score (i.e. using it as a band pass filter), but the scores above 50% are a lot more vague. Instead, I'd recommend sorting the dataset by score instead and setting a limit on the total number of images to select.

Top 100 images from a subset of danbooru2021 using the v1.7 model:

![AesPredv17_T100C]({{media}}/AesPredv17_T100C.jpg)

### Training

The training settings are initialized from the `config/CityAesthetics-v1.yaml` file (7e-6 LR, cosine scheduler, 100K steps).

![loss]({{media}}/loss.png)

Final dataset score distribution for v1.8:
```
3080 images in dataset.
  0 -   31 |
  1 -  162 |||||
  2 -  533 |||||||||||||||||
  3 -  675 |||||||||||||||||||||
  4 -  690 ||||||||||||||||||||||
  5 -  576 ||||||||||||||||||
  6 -  228 |||||||
  7 -   95 |||
  8 -   54 |
  9 -   29
 10 -    7
raw -    0
```

Version history:

- v1.0 - Initial test model with ~150 images to test viability
- v1.1 - Initialized top 5 score brackets with ~250 hand-picked images
- v1.2 - Manually scored ~2500 danbooru images for the main training set
- v1.3-v1.7 - Repeatedly ran the model against various datasets, adding the false negatives/positives to the training set to try and correct for various edgecases
- v1.8 - Added 3D and 2.5D images to the negative brackets to filter these as well
