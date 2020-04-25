---
title: OAuth2 with React
description: 
published: true
date: 2020-04-19T19:06:15.305Z
tags: 
---

# OAuth2 and React
There are a few libraries out there that seem to deal with this.

[Formidable Labs](https://formidable.com/) variant appears to be both active and supports some of the built in OAuth offerings on IOS and Android. So this might be the a good option:

* [React Native App Auth Git Repo](https://github.com/FormidableLabs/react-native-app-auth)
* [Google Example](https://github.com/FormidableLabs/react-native-app-auth/blob/master/docs/config-examples/google.md)

As I'm using my own google domain I can set up OAuth as an internal mechanism with low effort - so this looks promising.

## Update
After a review it appears Expo has this service built in, so we can enable this by calling:
```
expo install expo-google-app-auth
```
as per documentation [here](https://docs.expo.io/versions/latest/sdk/google/)





