---
title: Authentication Validator App
description: Simple test app that provides some test scenarios for validating OAuth2 authentication for users and services.
published: true
date: 2020-05-02T16:52:30.723Z
tags: 
---

# Authentication Validator Application
As part of setting up application authentication with Auth0 using OAuth2 and JWT I need a sandbox application with minimal features where I can prototype this approach and prove it works.

## Requirements
1. A front end service for user authentication. 
2. Two back-end services:
	1. One service will be used to test user and service JWT authenticaiton and role/permission data passed from Auth0. It will reply with success/fail and role information.
	2. One service will be used to obtain a JWT object from Auth0 and requets data from the related servce.
  
