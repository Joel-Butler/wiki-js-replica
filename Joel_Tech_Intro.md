---
title: Intro
description: 
published: true
date: 2020-09-06T19:10:37.407Z
tags: 
editor: markdown
---

# Kubernetes at Home with some Raspberry Pi's
I made this small wiki system as a way of experimenting with Kubernetes on a series of Raspberry Pi 4 units, and plan to use this site to document [lessons learned](/Platform/Lessons) and further experiments.

So far the [design](/Platform/Design) consists of a cluster of ~~3 nodes~~ 5 nodes (one shared master/worker node) and an NFS storage backend for stateful data such as the database powering this wiki and other stateful applications.

While not everything deployed to the stack can leverage the scalability and flexibilty of the kubernetes environment, my intent is to create a platform that houses not only my microservices but also tranditional applications as a complete toolkit for further development.

### Deployed so far
Current applications hosted in this cluster:
* [Gitea](https://git.jhbutler.info)  - an internal git repo and web front end (offline while I'm migrating NAS drives...)
* [wiki.js](https://wiki.jhbutler.info) - this wiki.
* mariadb - the backend database leveraged by the above applications.
* [osm](https://openservicemesh.io/) - which I think I *almost* have up and running. [Hitting some issues that still need to be resolved](/Platform/osm). Testing this required building out a [simple set of services in go](https://github.com/Joel-Butler/mesh-test). 
* [Jira](https://jira.jhbutler.info) - Java applications tend to be a bit more memory heavy but so far this appears to be running well using a Postgres back-end and a recompiled docker image courtesy of [Atlassian's docker library](https://bitbucket.org/atlassian-docker/docker-atlassian-jira/). We've licensed this and are looking at using  this to track some home projects as well as my work in this space. 


### Future Plans
* [Location Service](/Apps/FamilyLocation/Overview) - I'd like to replace the geolocation service we use on our family phones with an in-house option where I keep my data rather than some large company.

* [Authentication Validator](/Apps/AuthenticationValidator) - a prototype application testing User and API authentication (both of which I need to make this thing work well).



