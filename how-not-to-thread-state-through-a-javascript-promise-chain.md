---
layout: post
title: How (Not) To Thread State Through a JavaScript Promise Chain
category: ''
tags:
- GitHub Actions
- JavaScript
published: false

---
Last weekend I created a [GitHub Action](https://github.com/features/actions) and submitted it to the GitHub Hackathon. It was a relatively simple workflow that brings up a [WireMock](http://wiremock.org/) API for tests to run against.

It was pretty cool as I hadn't had a chance to write a Node application before. (The other way to write an Action is to build a Docker image, in which case any language can be used.)

In this post, I'm going to share about my thought process as I went about writing the Action.

Action repository: [https://github.com/williamhaw/setup-wiremock-action](https://github.com/williamhaw/setup-wiremock-action "https://github.com/williamhaw/setup-wiremock-action")

Tl;dr In JavaScript, use async await to deal with intermediate state, much simpler. Also, just having documentation from types make TypeScript awesome, even if you don't write your application in TypeScript. 

<!--excerpt-->