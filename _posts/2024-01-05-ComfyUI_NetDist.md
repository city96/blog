---
layout: post
title: "ComfyUI NetDist"
date: 2024-01-05 00:00:00 +0000
categories: update
---
{% assign media = "/" | append: page.path | replace: "_posts/","media/" | replace: ".md",""  %}

[Link to repository](https://github.com/city96/ComfyUI_NetDist) \| [Sample workflow](https://github.com/city96/ComfyUI_NetDist/files/13825326/NetDistSimple.json)

The NetDist node has received a major rewrite, and is now a lot more flexible for what you can do with it.

- It's now possible to load existing workflows from disk instead of being forced to use the currently active one.

- Distributing across a swarm of GPUs should also be less painful.

- It also has a "simple" node for easy dual-GPU setups.

- More to come later.

For more info, please see the readme!

<video muted autoplay controls width="100%">
    <source src="{{media}}/NetDist_2xspeed.webm" type="video/webm">
</video>
