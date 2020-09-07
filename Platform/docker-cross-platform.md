---
title: Cross Platform Development with Docker
description: 
published: true
date: 2020-09-07T14:00:44.879Z
tags: 
editor: markdown
---

## Overview
I'm frequently needing to rebuild (and sometimes recompile the software inside) docker images in order to have a compatible Arm64 image for the Raspberry Pi cluster. The Pi's themselves aren't *great* at compiling software - or at least can struggle with some of the more heavy workloads (I'm looking at you envoy).

With a mac-mini attached to the cluster with compute power to spare, I've opted to look into the Docker buildx features for cross platform compilation. 

## Docker buildx

[Adam Parco](https://medium.com/@adam.parco) created a [handy article on this feature](https://medium.com/@adam.parco/building-multi-arch-images-for-arm-and-x86-with-docker-desktop-864445865352) which was surprisingly straight forward. In essence the edge release allows you to either leverage qemu or a second docker node with the appropriate architecture to build for other architectures, and will then place the disticnt builds into a common manifest.

![snip-docker-cross-platform.png](/k8s/snip-docker-cross-platform.png)

The only catch I've found is that often as I build, I like to test the image locally. In order to do this you'll need to build normally as Docker doesn't like to push the other architectures into your local store (you need to push them and then run them from your repository). 