---
title: Up and running with Linkerd
description: 
published: true
date: 2021-01-24T17:40:10.117Z
tags: 
editor: markdown
dateCreated: 2021-01-24T17:40:10.117Z
---

## Overview
In my ongoing effort to secure my environment a mesh network has been a key objective to ensure that traffic is monitored and encrypted throughout the kubernetes cluster.

Istio-based offerings are really powerful, but ARM support is quite new and as a result most of the platforms built on top of this proxy are yet to truly have robust solutions.

Linkerd offered prebuilt ARM binaries for both client and server instances, and installation was quite painless. The most complicated step was in removing a custom resource definition that still existed from my previous OSM configurations. 

![linkerd-trafficsplits.png](/k8s/linkerd-trafficsplits.png)

After that enabling the mesh network was a matter of adding the appropriate annotation to each namespace, restarting and then ensuring the sidecars and monitoring data was being collected as expected.

The dashboard is quite helpful for monitoring and the metrics captured in grafana make for some useful insights into where things might need some work. 
