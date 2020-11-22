---
title: Authentication Architecture
description: Notes and details on authentication
published: true
date: 2020-11-22T13:26:33.926Z
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

