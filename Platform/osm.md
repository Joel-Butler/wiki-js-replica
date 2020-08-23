---
title: Deploying OSM
description: 
published: true
date: 2020-08-23T16:02:23.088Z
tags: 
editor: markdown
---

With some time off I decided to spend some time looking into a suitable mesh network to secure communication between pods. Istio appears to be the most mature and robust platform but has very limited support for Arm64 platforms (such as my raspberry pi cluster), and as it uses a [customized version of the envoy proxy](https://github.com/istio/proxy) there's a lot of work required to get things up and running.

Micrsoft just realeased a new offering in this space that is apparently much simpler called [Open Service Mesh](https://openservicemesh.io/). I thought I'd make a start at learning about mesh networks puzzling this out.

##Step 1 - A working Envoy proxy
[Envoy](https://www.envoyproxy.io/) also appears to have limited support for Arm64, but using the [handy guide from Steve Sloka](https://stevesloka.com/compile-envoy-on-raspberry-pi4/), and a few false starts I was able to successfully compile envoy's 1.15 release. A few tips if you're ever looking to do the same:
1. Bazel is a bit messy to compile and set up - ensure you have enough time set aside to get it up and running. There appears to be version compatibility issues between different releases, so I'd stick with the version envoy is looking for with your current release.

2. Ensure that you have sufficient swap space configured. My 4GB pi 4 was configured as part of a kubernetes cluster (with swap disabled) so I did need to ensure that I'd allocated sufficient swap space for the bazel compiler to work. 

##Step 2 - a working Osm deployment.
Like a lot of other kubernetes offerings OSM is a mix up of code and pre-published docker images. The code can be compiled for non-x86 systems, but the docker images used in the built process or embedded in code are x86 specific. 

As  a result I had to modify a bit of the OSM code to make thints work out. 

* Zipkin did not have a suitable Arm64 image.
* The OSM controller also did not have a suitable image.
* The helm charts responsible for installing the platform referenced an x86 only version of envoy.

To get this all working I set up a few new repositories in docker hub to hold the arm64 specific variants of thes systems.
1. [Zipkin](https://hub.docker.com/repository/docker/joelanz/zipkin)
2. 