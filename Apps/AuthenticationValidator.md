---
title: Authentication Validator App
description: Simple test app that provides some test scenarios for validating OAuth2 authentication for users and services.
published: true
date: 2020-10-17T15:24:18.658Z
tags: 
editor: markdown
dateCreated: 2020-05-02T16:52:30.723Z
---

# Authentication Validator Application

Update: after reading through some of the challenges with JWT and also hitting some concerns about the complexity of Node.js as a server environment I've opted to adjust my approach a bit here. I've kept the original notes below in section "[Old Thoughts](#Old Thoughts)".

## Approach
After looking through several options it appears the simplest approach here is to use a front end API server that will manage connectivity to back end services. I plan to use short lifecycle JWT tokens to communicate between the API server and back-end services. Given the small scale network and limited complexity, this may be overkill, but the point is to learn some stuff along the way :-)

### API Session host
Thankfully this bit uses old-school stuff. We authenticate a user, and store session data the traditional way. The user routes through the API server(s) for back end resources hosted as microservices, but that is invisible to the front-end consumer. I'll use a database for session storage to ensure I can load balance across multiple API instances (otherwise what's the point of all this stuff).

Once I get OSM up and running I can further secure communication between the front and back-end hosts. Given these are literally a single layer 2 hop away from each other, this isn't a huge risk - but I'd like to address it as soon as is feasible anyway. I don't like gaps. 

![sandbox-auth.png](/apps/sandbox/sandbox-auth.png)

### Authentication
Authentication will be through a traditional app authentication cycle. As the family is all on google I'll start with that as my authentication source, the less credentials I need to store the better :-)

### First step - Can I authenticate and consume a service	
Using the Sandbox repo, can I set up a front end API service that can authenticate users via local authentication and via google authentication if time permits. After authentication, can I then allow only authorized users to consume a back-end number generation service (no JWT yet).

### Second step - Can I make the service limit who should access. 
Using the sandbox repo, can I set up short term JWT session tokens between the front end API and back end service. 

Expected outcome: If I expose the service stand-alone, it should forbid access, while access through the API should be allowed (ensuring the API manages control to the service, and the service itself is capable of quickly assessing and denying other connections).

### More to come...
Plenty more to do here but that's a good enough start.


## Old Thoughts

As part of setting up application authentication with Auth0 using OAuth2 and JWT I need a sandbox application with minimal features where I can prototype this approach and [prove it works](Lessons).

## Requirements
1. A front end service for user authentication. 
2. Two back-end services:
	1. One service will be used to test user and service JWT authenticaiton and role/permission data passed from Auth0. It will reply with success/fail and role information.
	2. One service will be used to obtain a JWT object from Auth0 and requets data from the related servce.

![ink_(3).png](/ink_(3).png)
(the diagram is a bit rough, and technically the reply goes from Auth0 to the user, but you get the idea...)
