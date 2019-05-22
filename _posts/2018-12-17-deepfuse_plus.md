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

Ghosting remains the biggest problem in MEF. Ghosting is the appearance of unwanted artifacts due to misalignment of the input LDR images. This happens due to object or camera motion in the input image(s). This problem is more pronounced with extreme exposure input images. Patch-based synthesis methods were used to generate details in such regions. These techniques, despite working even for occluded pixels, are computationally expensive and fail for complex scenarios. Prior deep learning methods work effectively, but do not scale to an arbitrary number of images. Additionally, these methods lack speed. 

## The Idea and The Process

Our pipeline consists of the following sequential steps:

1. <b>Registration:</b> We first register the input images using optical flow. We do this to cancel out any relative motion between the input LDR images. To calculate optical flow, we make use of the lightweight PWC-Net, developed by the good folks at NVIDIA, due to its high accuracy and low execution time. Since optical flow requires the brightness of the input images to match, we raise the exposure of the darker image to match that of the lighter image.

2. <b>Refinement:</b> Due to errors in optical flow estimation and warping, the registered image is prone to artifacts. We utilize a U-Net style network, which takes as input the warped image, the input image unaligned image, and optical flow (all stacked along the depth dimension to obtain 8 channel input). The network outputs a weight map in the range [0, 1], which is then used to obtain the final perfectly registered image.

3. <b>Fusion:</b> Since both the above networks work on image pairs, they can be extended to an arbitrary number of images. The challenge is to extend the fusion network to work with an arbitrary number of images. Our strategy for fusing an arbitrary number of images is simple. We first extract exposure-invariant features from each image using an encoder network. To merge the features, we first calculate the minimum and maximum maps of all the feature maps. We concatenate these two maps, and then pass it to a decoder to generate the final fused image. The encoder-decoder architecture is constructed using skip connections. The novel feature merging strategy ensures that our network can fuse any number of images. 

![Fusion](/assets/img/deepfuse_fusion_net.png)

<center><b>Fusion:</b> Our Proposed Fusion Network.</center>

Except the optical flow network (which is pretrained), we utilize a combination of L2 loss, Gradient Loss, and Multi-Scale SSIM loss to train the networks. Additionally, we also captured and carefully curated a set of 582 high resolution sequences to train our network. We plan to make this dataset publicly available soon.

## The Results

Quantitatively, our network performs at par or better than other algorithms in literature in terms of both PSNR and SSIM.

![Quantitative](/assets/img/quantitativeresult.png)

Qualitatively, our results display a notable absence of unwanted ghosting artifacts when compared to other methods.

![Qualitative](/assets/img/deepfuse_results.png)

In terms of speed, our network only slightly lags behind Wu et. al., while providing better results.

![Speed](/assets/img/speedresult.png)


## What's next?

The fast nature of the network enables us to consider this algorithm for HDR video generation. To accomplish this, structural and temporal coherency have to be maintained. Another idea would be to train this network using an NR-IQA metric such as MEF-SSIM, to enable the paradigm shift from supervised to unsupervised HDR fusion.  