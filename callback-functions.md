# Callback functions

## Foundational concepts

Synchronous programming model
- things happen one at a time
- a long-running action will return only when the action is finished

Asynchronous programming model
- allows multiple things to happen at the same time
- when an action is running, the program does not wait to start the next action
- It starts an additional "thread" (another running program whose execution may be interleaved with other programs)

## Callbacks

**TL;DR** 
- An approach to asynchronous programming.
- Functions that perform a slow action take an extra argument, a callback function.
- The action is started, and when it finishes, the callback function is called with the result.

**Example: setTimeout**

```
setTimeout(() => console.log("Tick"), 500);
```
This example waits a certain number of milliseconds. Once it waits the given number of milliseconds, it calls a function.


## When would we use it? 

- Use setTimeout when doing something like udpating an animation or checking whether something its taking longer than a given amount of time.
