---
title: Authentication Architecture
description: Notes and details on authentication
published: true
date: 2021-01-02T16:54:31.446Z
tags: auth0, authentication, k8s
editor: markdown
dateCreated: 2020-05-02T14:57:20.468Z
---

# Authentication

## Overview
Authentication is complex. My initial design for a microservice platform is to establish a small microcosm of systems that interact while being sure:
1. Each system can filter requests so that only *allowed* clients can access a given service. 
2. For users, we can establish who each client is and provide some level of session and unique service experience based on the users identity. 

This is all pretty common, but in a microservices environment we want to establish this with the minimum amount of overhead for each tiny service, while also keeping things secure. 

## The First (failed) Approach
*If it sounds too simple to be true - it probably is.*

JWT appeared to be a good defacto standard for microservice access. There is good support for the process and it is relatively simple and straight forward.

Unfortunately a key flaw in this methodology is that JWT objects are not revokable (they can time out, but that is about it), and once issued information cannot be updated without issuing a new token. 

As a result I believe the ideal use for these tokens is as the back-end to a front end gateway based authentication service. This gives a lightweight method for access to the back-end services and we can establish a much lower threshold for token renewal internal to the kubernetes cluster.

For Clients, we will likely use a standard OAuth2 authentication with a few back-ends for good measure (for now I'm going to set up Google). 

## 2nd Attempt: A partial success
When exploring OAuth2 options, I had started investigating building my own router and authentication engine, but in reality a lot of these things already exist in seperate consumable items. Kubernetes can handle routing via Ingress, and I have a working ingress engine in [ingress-nginx](https://kubernetes.github.io/ingress-nginx/). In investigating this in more detail it appeared that this challenge was already solved at least in part by a microservice [Oauth2-proxy](https://github.com/oauth2-proxy/oauth2-proxy). The approach is surprisingly straight forward. 

Kubernetes directs Nginx via some additional ingress annotations (and a second limited ingress solely used for authentication) to use the OAuth2-proxy micro-service for authentication. Provided the authentication is successful the ingress engine forwards traffic to the back-end services, and passes basic identity information as some of the **X-Forwarded** header information, which I can then extract. 

The system did require the use of [Redis](https://redis.io/) for storage of the Oauth2 data, so I now have a *limited* deployment of redis available in the cluster (it's not quite as robust or [Highly Available](https://redis.io/topics/sentinel) as I'd like yet - [more on that later](Redis) once I've had time to document it.

Some additional testing is required before I'm completely confident in this approach (I'd like to ensure trust between the ingress and back-end services to ensure that this process cannot be bypassed as a security exploit), but it is a start and its simplicity is quite attractive. 

## Authentication Scenarios

### Client to API

In this instance I'd like to authenticate clients against my goolgle domain, using OAuth2. Front end authentication may require a new service, or it might be somethign I can introduce into the ingress controller for end users, but what I **don't** want is to have to configure authentication at each individual service.

### API to API

Internally I need a way to validate API to API calls, and to potentially expose some API access to external server based systems (no plans yet, but having the ability to do this seems prudent).

## Solutions
While there are some good external solutions out there, I think the simplest approach here is to build a dedicated authentication microservice that is able to both authenticate users and generate internal JWT tokens for use with back-end services. While we could build this into the front end Nginx gateway, I think it might be prudent to keep the authentication layer further away from the internet, so we'll place this an internal router in between the gateway and back end services. 

As a result this service will need to be scaleable:
* Sessions should be stored in a highly responsive database accessible from multiple clients.
* Actions should be atomic (a client should be able to hop between instances of the service with no impact to function or performance).

