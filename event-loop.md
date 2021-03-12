# The Event Loop

## Why do we care about the event loop?
- JavaScript execution flow is based on an `event loop`. 
- Understanding _event loops_ helps developers optimize JS and select the best architecture. 

## The Event Loop

**The event loop:**
In JavaScript there is an **endless loop** -  where the JS engine waits for tasks, executes them, and then sleeps (waiting for more tasks).

**The algorithm:**
1. While there are tasks: 
  - execute them, starting with the oldest task.
2. Sleep until a task appears
  - return to step 1

**Tasks**
Most of the time JS is "sleeping". It only runs if a script/handler/event task activates the event loop.
- Tasks are set – the engine handles them – then waits for more tasks (while sleeping and consuming close to zero CPU).
- Examples of tasks: 
  - when an external script loads, the task is to execute it
  - when a user moves their mouse, the task is to dispatch `mousemove` event 
  - when a `setTimeout` reaches the end of the time, the task is to run the callback function

If a task comes when the JS engine is already busy, it is enqueued.
- tasks for a queue (often called the "macrotask queue")
- Example: The engine is downloading a `script`, but a user moves their mouse, triggering a `mousemove` event, and `setTimeout` is finished. These stack on each other in a queue and are processed on a "first come first serve basis" 
  - load `script` >> `mousemove` >> `setTimeout`

There are two important details to remember:
- Rendering never happens while the engine is executing a task. Changes to th DOM are painted only _after_ the task is complete.
- If a task takes too long, the browser can't do other tasks (like processing user events) so after awhile it raises an alert like "Page Unresponsive"
## Resources
- https://javascript.info/event-loop
