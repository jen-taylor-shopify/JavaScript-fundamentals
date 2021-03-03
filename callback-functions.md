# Callback functions

## Foundational concepts

**Synchronous programming model**
- things happen one at a time
- a long-running action will return only when the action is finished

**Asynchronous programming model**
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

It becomes less useful if you perform multiple async actions in a row
- this is because you have to keep passing new functions in to handle the continuation of the computation after the action

Asyncronicity is _contagious_
- any function that calls a function that works asynchronously must itself be asynchronous and use a callback (or similar mechanism) to deliver a result

## Callbacks

### “callback-based” asynchronous programming

**Callback-based asynchronous programming:**
A function that does something asynchronously should provide a callback argument where we put the function to run after it’s complete.

Say we want to append a script to the document and load a script with a given source. You might write something like this: 
```
function loadScript(src) {
  let script = document.createElement('script');
  script.src = src;
  document.head.append(script);
}
```

You could call it in your code like this: 
```
loadScript('/my/script.js');
...
newFunction();
```
The `loadScript()` function is executed “asynchronously” -> it starts loading now, but runs later, when the function has already finished. Any code below `loadScript()` will not wait until its finished. 

This means that if `loadScript();` declares new functions, like `newFunction()`, we can't do this until `loadScript` is finished. We'll end up getting the following error: 
```
loadScript('/my/script.js'); // the script has "function newFunction() {…}"

newFunction(); // no such function!
```
So how do we know when `loadScript()` runs so we can use new functions?
- We can add a callback function as a second argument to `loadScript()` that should execute when the script loads!

```
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(script);

  document.head.append(script);
}
```

Then we can call the `newfunction()` from the script that will only be ready to go when the `loadScript()` is finished.
```
loadScript('/my/script.js', function() {
  // the callback runs after the script is loaded
  newFunction(); // so now it works
  ...
});
```
The second argument is a function (usually but not always anonymous) that runs when the action has completed, to make sure that everything executes in the correct order.

### “error-first callback” 

**Error-first callbacks:**
- The first argument of the callback is reserved for an error if it occurs. Then callback(err) is called.
- The second argument is for the successful result. Then callback(null, result1, result2…) is called.
- This structure means that a **single callback function** is used both for reporting errors and passing back results.

```
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}
```
The above code calls 
- `callback(null, script)` for success
- `callback(new Error(Script load error for ${src}));` for an error

Then we can handle each path with the following code: 
```
loadScript('/my/script.js', function(error, script) {
  if (error) {
    // handle error
  } else {
    // script loaded successfully
  }
});
```


## What are the benefits? 




## When would we use it? 
- Use setTimeout when doing something like updating an animation or checking whether something its taking longer than a given amount of time.
- 

## What are the drawbacks? 

### Error-prone
- Calling a callback is more error-prone than simply returning a value
- Needing to structure large parts of a program with callbacks isn't ideal


### Callback hell / Pyramid of Doom
If you wanted to load a script, and then load another one when that one has finished loading, you can nest the call insaide a callback. 

But when this is used for asynchronous actions that follow one after another, you quickly get into "the pyramid of doom" or "callback hell".

```
loadScript('1.js', function(error, script) {

  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', function(error, script) {
      if (error) {
        handleError(error);
      } else {
        // ...
        loadScript('3.js', function(error, script) {
          if (error) {
            handleError(error);
          } else {
            // ...continue after all scripts are loaded (*)
          }
        });

      }
    });
  }
});
```
- The deeper your go, the more difficult it is to manage!

One way to handle this is to keep every callback a separate top-level function: 
```
loadScript('1.js', step1);

function step1(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', step2);
  }
}

function step2(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('3.js', step3);
  }
}

function step3(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...continue after all scripts are loaded (*)
  }
}
```

But this is also repetitive and hard to read/follow if you're not familiar with the code. 
This is an instance better suited for a **promise**.

## References
- https://eloquentjavascript.net/11_async.html
- https://javascript.info/callbacks
- 
