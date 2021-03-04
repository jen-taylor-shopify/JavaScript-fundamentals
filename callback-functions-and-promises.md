# Callback functions

## Foundational concepts

**Synchronous programming model**
- you run some code, and the result is returned as soon as the browser can do so
- JavaScript is **single threaded** so only one thing can happen on a thread at a time and everything else is blocked until that operation completes
- while each operation is being processed, nothing else can happen — rendering is paused

**Asynchronous programming model**
- allows multiple things to happen at the same time
- when an action is running, the program does not wait to start the next action
- it starts an additional "thread" (another running program whose execution may be interleaved with other programs)

**Event Queue**
- async operations (like promises) are put into an **event queue**
- The event queue runs after the main thread has finished processing so that they do not block subsequent JavaScript code from running 
- The queued operations will complete as soon as possible then return their results to the JavaScript environment

**2 Types of asynchronous code:**
- old-style callbacks
- promise-style code

## Callbacks

**TL;DR** 
- A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action.
- The function is expected to "call back" (execute) the function argument at a given time.
- They are often used to continue code execution after an asynchronous operation has completed.

**Example: An asynchronous callback**
```
document.queryselector("#callback-btn")
    .addEventListener("click", function() {    
      console.log("User has clicked on the button!");
});
```
- This example waits until it observes the `click` event. 
- Only once it hears the click event will it run the second argument's code.

It becomes less useful if you perform multiple async actions in a row
- this is because you have to keep passing new functions in to handle the continuation of the computation after the action

**Asyncronicity is _contagious_**
- any function that calls a function that works asynchronously must itself be asynchronous and use a callback (or similar mechanism) to deliver a result.
- 

**Example: Another asynchronous callback**
```
const btn = document.querySelector('button');

btn.addEventListener('click', () => {
  alert('You clicked me!');

  let pElem = document.createElement('p');
  pElem.textContent = 'This is a newly-added paragraph.';
  document.body.appendChild(pElem);
});
```
- The first argument `click` is the event listened for, and the second argument is a `callback function` that is invoked when the event is fired.
- When we pass a callback function as an argument to another function, we are only passing the **function's reference** as an argument (it won't be executed immediately)
- Its "called back" when the containing function is ready.

**Example: One more asynchronous callback**
Say we want to append a script to the document and load a script with a given source. 
You might write something like this: 
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

**Example: Error-first callbacks**
- The first argument of the callback is reserved for an error if it occurs. Then `callback(err)` is called.
- The second argument is for the successful result. Then `callback(null, result1, result2…)` is called.
- This structure means that a **single callback function** is used both for reporting errors _and_ passing back results.

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

**Example: Synchronous callbacks (`forEach()`)**
- Not all callbacks are async — some run synchronously. 
- An example is when we use `Array.prototype.forEach()` to loop through the items in an array
- The `forEach()` function calls a `callback` that you provide once for every element in an array in ascending order.  

```
const gods = ['Apollo', 'Artemis', 'Ares', 'Zeus'];

gods.forEach(function (eachName, index){
  console.log(index + '. ' + eachName);
});
```
- A `forEach()` expects the paramater passed into it to be a callback function. This `callback` it accepts two arguments (`name` and `index`)
- Unlike `map()`, which returns a new array, `forEach()` will always return `undefined`
- It doesn't wait for anything to finish before running - it runs immediately

## What are the benefits of callback functions? 

**Single threaded operations**
- Callbacks allow single-threaded operations (like Javascript) to execute asynchronously.

**Order control**
- You can exercise more control over the order in which functions are run and what data is passed between them.
- This is especially useful when you are waiting on something external to be retrieved before executing on it.
- You can also defer some processing until run time based on user inputs

**Access to scope** 
- Callback functions have access to both their own scope plus the scope of the code that calls them plus the global scope of the calling code. 
- This is useful in larger applicatoins where data is passed across several module boundaries during execution of a task.

## What are the drawbacks of callback functions? 

**Nested individual error handling**
- Error handling/failure callbacks need to be called once for each level of nesting

**Callback hell / Pyramid of Doom**
- If you wanted to load a script, and then load another one when that one has finished loading, you can nest the call inside a callback. 
- But when this is used for asynchronous actions that follow one after another, you quickly get into "the pyramid of doom" or "callback hell".
Example: 
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

- One way to handle this is to keep every callback a separate top-level function: 
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

## Promises

- **Promises** are the new style of async code in modern Web APIs.
- **The Promise object** represents the eventual completion (or failure) of an asynchronous operation and its resulting value.

**In other words:**
- Unlike callbacks, promises takes a _single_ parameter
- It returns a `promise`, which is like an _intermediate state_
- It's the browser saying "I promise to get back to you with the answer as soon as I can"
- Eventually when the response arrives the promise is fulfilled <3 

**Callback vs Promise**
- Callbacks are functions that are passed to another function
- Promises are a returned object that you attach a callback function to

**Example: `fetch()` API**
- The `fetch()` API is a modern, more efficient version of `XMLHttpRequest`.
- The `fetch()` method of the `WindowOrWorkerGlobalScope` mixin _starts_ the process of fetching a resource, and returns a promise that will be fulfilled once the response is available.
- When the response object is available the promise is resolved

```
fetch('products.json').then(function(response) {
  return response.json();
}).then(function(json) {
  products = json;
  initialize();
}).catch(function(err) {
  console.log('Fetch problem: ' + err.message);
});
```
- `fetch()` requests the `products.json` resource, and _promises_ to eventually get back to you with the resource as soon as it can
- `.then()` is chained onto the `fetch()`. 
  -  The `then()` method also returns a promise! When `.then()`'s promise is fulfilled or rejected, it returns a value.
  - The first `.then()` method  contains a callback function that will run if the previous operation is successful and takes the result of the `fetch()` as its argument.
  - ANOTHER `.then()` block is chained onto the previous `.then()` block. It is also a callback function that takes the result of the previous `.then()` function as an argument.
  - Thats 3 chained promises!
- the `.catch` block at the end runs if any of the `.then()` blocks fail

## What are the benefits?
- Promises are specifically made for handling async operations, and have many advantages over old-style callbacks:
  - **Chaining**: You can chain multiple async operations together using `.then()` methods. If you tried to do the same thing with a callback function, it would result in a Pyramid of Doom
  - **Strict Order**: Promise callbacks are always called in the strict order they are placed in the event queue
  - **Error handling**: Its much easier ot handle errors with the **.catch()** block instead of handling them at each level of the callback function
  - **Inversion control**: callbacks lose full control of how the function will be executed when passing a callback to a third-part library



## What are the drawbacks? 

## When would you use it?

## When would you not use it?



## References
- https://eloquentjavascript.net/11_async.html
- https://javascript.info/callbacks
- 
