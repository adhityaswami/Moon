---
layout: post
title:  "DeepFuse++"
date:   2018-12-17
excerpt: "Deep Deghosting for Extreme Exposure Fusion"
project: true
tags: [deep, hdr, exposure, fusion, deghosting]
comments: false
---

![Architecture](/assets/img/deepfuse_arch.png)

<center><b>DeepFuse++:</b> Our Proposed Architecture.</center>

*This paper has been accepted to the 2019 IEEE Conference on Computational Photography, Tokyo. Due to certain restrictions, we are unable to provide an open access version of it at the moment.*

I worked on this project during the summer of 2018, under the guidance of Prof. Venkatesh Babu at Indian Institute of Science, Bangalore. Our aim was to develop a deep Exposure Fusion strategy that worked on an arbitrary number of images, with an emphasis on speed and accuracy.

## The Problem

Commercial cameras usually have a limited dynamic range. Because of this, they cannot capture details in extreme lighting conditions, which appear saturated. Multi-Exposure Fusion is a strategy to obtain apparent High Dynamic Range (HDR) images from a number of Low Dynamic Range (LDR) images. The result of MEF is a visually appealing LDR image that appears to possess higher dynamic range.

![HDR](/assets/img/hdr_example.png)

<center><b>MEF:</b> Two LDR images are fused to produce a visually appealing pseudo-HDR image. Saturation is absent, since details are picked up from appropriate inputs.</center>