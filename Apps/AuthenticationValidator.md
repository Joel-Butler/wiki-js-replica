---
title: Authentication Validator App
description: Simple test app that provides some test scenarios for validating OAuth2 authentication for users and services.
published: true
date: 2020-10-17T15:08:15.714Z
tags: 
editor: markdown
dateCreated: 2020-05-02T16:52:30.723Z
---

# Authentication Validator Application

Update: after reading through some of the challenges with JWT and also hitting some concerns about the complexity of Node.js as a server environment I've opted to adjust my approach a bit here. I've kept the original notes below in section "old thoughts".

## Approach
After looking through several options it appears the simplest approach here is to use a front end API server that will manage connectivity to back end services. I plan to use short lifecycle JWT tokens to communicate between the API server and back-end services. Given the small scale network and limited complexity, this may be overkill, but the point is to learn some stuff along the way :-)

### API Session host
Thankfully this bit uses old-school stuff. We authenticate a user, and store session data the traditional way. The user routes through the API server(s) for back end resources hosted as microservices, but that is invisible to the front-end consumer. I'll use a database for session storage to ensure I can load balance across multiple API instances (otherwise what's the point of all this stuff).



## Old Thoughts

As part of setting up application authentication with Auth0 using OAuth2 and JWT I need a sandbox application with minimal features where I can prototype this approach and [prove it works](Lessons).

## Requirements
1. A front end service for user authentication. 
2. Two back-end services:
	1. One service will be used to test user and service JWT authenticaiton and role/permission data passed from Auth0. It will reply with success/fail and role information.
	2. One service will be used to obtain a JWT object from Auth0 and requets data from the related servce.

![ink_(3).png](/ink_(3).png)
(the diagram is a bit rough, and technically the reply goes from Auth0 to the user, but you get the idea...)
