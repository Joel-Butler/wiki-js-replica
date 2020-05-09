---
title: Authentication Validator - lessons
description: 
published: true
date: 2020-05-09T16:52:42.625Z
tags: 
---

# Lessons Learned

## Expo/React.js

Because expo is effectively a wrapper app around a web package, there are some limitations in testing authentication through this system. In the shared app, expo has to authenticate via a clumsy interaction with a web browser. In pure Android/IOS apps the system is able to leverage an existing account in a much more seamless way. 

In terms of user experience this isn't a deal breaker, but things could be neater. This is a trade-off between the usabilty of expo and all the baggage that comes along with an 'on-rails' system. 

## Auth0

Auth0 worked really well. Integration with Google was well documented and the platform works as expected. The beauty of tying this and the Expo framework together is that you could easily enable many authentication options with no real change to the platform. Auth0 remains the master of record and passes information to the app. I think this one is a keeper. 

## JWT

I had mistakenly thought JWT might have been a passport-like option for cross application authentication, but in digging through the logic of how these tokens are created, stored and utilized it became clear rather quickly that these are meant to be short-term tokens for use (like a half hour wrist pass to theme park rides). 

As long as the APIs are part of the same domain/application the token is useful. If they are part of a different application, you need a new token.

Using my theme park token, it would be like me trying to use take my half day pass from Disney and attempting to use this at Universal Studios... I would need a different pass. 


## Application considerations

So for authentication **within** my app, all APIs need to be at the same external endpoint (they can be on different internal endpoints inside the kubernetes cluster), and need to be able to decode and validate the same JWT tokens - so this would require a common secret -> which in turn would work best if the application was contained within a single namespace.

... That all seems sensible so far. So from here, I think I need to revise my authenticator design, make sure it all works and then I have a platform for my expo app.

