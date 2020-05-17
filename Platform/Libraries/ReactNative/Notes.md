---
title: React Native Notes
description: Notes on React Native I've come across so far
published: true
date: 2020-05-17T20:11:50.506Z
tags: 
---

# Overview
Notes on my experiences with React Native so far.

## On State and Hooks
React is rather strict about how application state is managed. Components should be considered effectively static, only their properties (props) change. Therefore state needs to either be held in a part of the component heirarchy that makes it accessible to the appropraite children, or in a global location.

Hooks appear to provide a useful way for defining state, but I found these to be rather complex. 

## Redux - a complimentary state library
Rather than puzzle through the complexities of Hooks at this point it looked like a better use of time would be to [investigate Redux](https://redux.js.org/introduction/getting-started) - this is no less complex, but offers a well defined and managed method for managing state in an application, including some very useful debugging tools. 

Redux uses a single 'store', in which we define actions (as intent to do stuff) and then code reducers linked to these actions that actually do the doing. The store becomes a central dispatcher of these activities and can also provide feedback on activities to registered listeners.

![redux-scrawls.png](/redux-scrawls.png)


## IOS and Android challenges
Haven't had too many of these yet, but I expect them to occur soon :-)
