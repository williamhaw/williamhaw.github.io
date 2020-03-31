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

In this post, I'm going to ~~share my thought process~~ bemoan my follies as I went about writing the Action.

Action repository: [https://github.com/williamhaw/setup-wiremock-action](https://github.com/williamhaw/setup-wiremock-action "https://github.com/williamhaw/setup-wiremock-action")

Tl;dr In JavaScript, use async await to deal with intermediate state, much simpler. Also, just having documentation from types make TypeScript awesome, even if you don't write your application in TypeScript.

<!--excerpt-->

# Pseudocode

1. Download the WireMock jar.
2. Copy stubs (request and response definitions) to folders read by WireMock when it starts.
3. Start WireMock in the background.
4. Ping WireMock (to make sure it has started).
5. Run test command.
6. Cleanup
   1. Kill WireMock
   2. Get the WireMock logs (these contain request mismatches, very important when debugging).
   3. Delete copied stubs.
   4. Tell Github if this run failed.

I started out stubbing all the functions and deciding on their inputs and output. I noticed:

* To start WireMock, I need the path where I downloaded it to.
* To kill the WireMock process, I need the process object from when I start it.
* To tell GitHub that the job failed, I need the status from pinging WireMock and from running the test command.
* These steps need to run in the order above.

What all these have in common is that

At this point I could either store the required values in global state or find an abstraction that allows me to be able to retrieve state that needs to span across multiple steps.

At this point I read up on Javascript Promises and realized that I could use a chain of Promises to enforce the execution order without maintaining global state. Just one problem:

```javascript
//promises are chained like this
promiseReturningFunctionOne() //say this returns an object
  .then(promiseReturningFunctionTwo) //this function takes one input and returns a Promise
  .then(state => promiseReturningFunctionThree(state)) // this is more explicit passing of state
  .then({myValue} => promiseReturningFunctionThree(myValue)) //to extract one value out of the preceeding state
  .catch(e => {console.error(e)}) // handle all errors, processing stops at the first function that throws an error
```

The state that each function gets is from the one immediately preceding it; that `state` object is _not_ the same as the one wrapping `myValue.`

After thinking about it, I came up with something like this:

```javascript
downloadWireMock()
  .then(state => {
    copyStubs(); //perform side effect, doesn't return any value
    return state;
  })
  .then(state => {
    const wiremockProcessState = startWireMock();
    //merge state so that later functions have access
    return {
      ...state,
      ...wiremockProcessState
    }
  })
  .then(state => {console.log(`State after WireMock started: ${state}`)})
  ...
```

# The Good

* It's vanilla JavaScript (ES6).
* It extracts the state handling and organises the main logic as a flow. The structure of the app can be understood by reading top to bottom.
* The various helper functions don't need to know about the rest of the flow.
* The entire state of the application can be easily logged by sticking a `then` clause in between any two steps (second-last line above).

# The Bad

* There's a lot of boilerplate. Instead of one line to add a function to the chain, it's 3-4 lines. For every function.
* Every non side-effecting function needs to return an object so that it can be merged into the previous state.

# The Ugly

* Did I say boilerplate?