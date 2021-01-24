---
title: Intro
description: 
published: true
date: 2021-01-24T17:23:50.273Z
tags: 
editor: markdown
dateCreated: 2020-08-02T17:05:10.667Z
---

# Kubernetes at Home with some Raspberry Pi's
I made this small wiki system as a way of experimenting with Kubernetes on a series of Raspberry Pi 4 units, and plan to use this site to document [lessons learned](/Platform/Lessons) and further experiments.

So far the [design](/Platform/Design) consists of a cluster of ~~3 nodes~~ 5 nodes (one shared master/worker node) and an NFS storage backend for stateful data such as the database powering this wiki and other stateful applications.

While not everything deployed to the stack can leverage the scalability and flexibilty of the kubernetes environment, my intent is to create a platform that houses not only my microservices but also tranditional applications as a complete toolkit for further development.

### Deployed so far
Current applications/systems hosted in this cluster:
* [Gitea](https://git.jhbutler.info)  - an internal git repo and web front end (offline while I'm migrating NAS drives...)
* [wiki.js](https://wiki.jhbutler.info) - this wiki.
* mariadb - the backend database leveraged by the above applications.
* [linkerd](https://linkerd.io/) - after a few failed attempts with the less production ready [osm](https://openservicemesh.io/), I noticed that Linkerd claimed to have binaries pre-built for ARM chipsets. Given the weeks of mucking around with OSM, [installing linkerd](/Platform/linkerd-mesh) in about 2 hours was quite a nice experience. Currently the mesh network is enabled for most of my key services (databases, micro-services and ingress). 


### Future Plans
* [The Sandbox](Apps/Sandbox/Overview) - This is the testing location for all microservice 'stuff' I'd like to have up and running. Right now the focus is on [Authentication](/Platform/authentication), with some changes in approch documented [here](/Apps/AuthenticationValidator).

* [Location Service](/Apps/FamilyLocation/Overview) - I'd like to replace the geolocation service we use on our family phones with an in-house option where I keep my data rather than some large company.

* [Game Night App](/Apps/GameNight) an phone and tablet based interactive UI for some fun with my family. 



