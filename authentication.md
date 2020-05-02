---
title: Authentication Architecture
description: Notes and details on authentication
published: true
date: 2020-05-02T14:57:20.468Z
tags: k8s, authentication, auth0
---

# Authentication

## Overview
Authentication is complex. Looking into the manner most microservice platforms manage this, JWT appears to be the primary method of determining whether a front-end user or back-end service is identified and permitted to access a given component within the platform.

There are many approaches to this, but as far as my design goes I believe authentication should occur centrally, permissions against identities should be defined within the microservice or via a network layer middleware.

Given the limited memory and compute space available to me in my small cluster, I'm not terribly keen to introduce new layers to manage authentication. Instead I'll aim for a common library for per-service validation and see if we can leverage kubernetes secrets to define at container boot who should have access to services and sub paths. 

## Authentication Scenarios

### Client to API

In this instance I'd like to authenticate clients against my goolgle domain, using OAuth2. Front end authentication may require a new service, or it might be somethign I can introduce into the ingress controller for end users, but what I **don't** want is to have to configure authentication at each individual service.

### API to API

Internally I need a way to validate API to API calls, and to potentially expose some API access to external server based systems (no plans yet, but having the ability to do this seems prudent).

### JWT - potential mechanism

JWT appears to be a common method to allow APIs to validate identity after successful authentication and have some fairly good libraries available across many software platforms. So I think we'll go with this option.


## First attemptL Auth0

In looking into authentication solutions I stumbled on Auth0 - a commercial offering that provides authentication as a service, integrating into Google and other social authentication mechanisms while also providing multiple authentication mechanisms under the hood.

Given that this is a one-man project right now, managing authenticaiton compliance is something I'm unlikely to be able to keep on top of, so going to the experts seems like a good move.

Auth0 has [a good example of an API and Mobile app combination](https://auth0.com/docs/architecture-scenarios/mobile-api/part-1) that should be a good place to start reading and building some knowledge.



