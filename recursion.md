# Recursion

## What is it?

### TL:DR;

- When a function solves a task, it can call other functions in the process. And when a function calls _itself_, its called _recursion_.
- So, recursion is a programming pattern that means calling a function from itself. (This pattern is not exclusive to JavaScript).
- It is used to write _elegant_ code (simple and easy to maintain).

### How does it work? 

There are two ways to accomplish the same task - iterative and recursive.

#### Iterative approach

##### What 
Uses a `for` loop to iterate over the result multiple times.

```
function pow(x, n) {
  let result = 1;

  for (let i = 0; i < n; i++) {
    result *= x;
  }

  return result;
}

console.log( pow(2, 3) ); // 8
```

##### How  

A `for` loop uses a loop algorithm to execute a `for` statement part-by-part. 

The statement `for (let i = 0; i < 3; i++)` results in the following executions: 
- begin `i = 0`: this executes once when the loop is entered
- condition `i < n`: the `condition` is checked before every iteration of the loop. If it is false, the loop stops.
- body `result *= x`: the `body` runs each time the `condition` is truthy.
- step `i++` executes after the `body` on each iteration

Which step-by-step looks like this for `(pow 2, 3)`: 
- Execution context stores `x = 2, n = 3` 
- Run `begin: i = 0`
- Check `condition: i < n`
- Run `body`
- Run `step`
- Check `condition: i < n`
- Run `body`
- Run `step`
- Check `condition: i < n`
- Run `body`
- Run `step`
- Check `condition: i < n`
- End loop, `return` the result.  


#### Recursive approach

##### What 
Uses a function that (recursively) calls _itself_ multiple times.

```
function pow(x, n) {
  if (n == 1) {
    return x;
  } else {
    return x * pow(x, n - 1);
  }
}

console.log( pow(2, 3) ); // 8
```

##### How  

A recursive approach is fundamentally different than a `loop`. 
When the function is called it splits into two branches:
- 1. base: if `(n == 1)` then it immediately knows that the result will be `x`. Nothing else needs to be executed. 
- 2. recursive step: if the first statement is false, then we can create a simple action that causes the function to call itself, reducing `n` until it reaches 1.

**An important note:** 
- Every function has one execution context associated with it. 
- When a function makes a nested call:
   -  the current function is paused 
   -  the execution context it put into the execution context stack
   -  The nested call executes
   -  The nested call ends and the old execution context is retrieved from the stack
   -  The outer function continues from where it paused

Which step-by-step looks like this for `(pow 2, 3)`: 
- Execution Context stores `x = 2, n = 3`
- Check `condition: n == 1` (which is falsey)
- Runs body of the `else` statement which makes a nested call. This pauses the current execution context, and adds it to the stack. 
- A new execution context is created in the subcall with `x = 2, n = 2`
- Check `condition: n == 1` (which is falsey)
- Runs body of the `else` statement which makes a nested call. This pauses the current execution context, and adds it to the stack. 
- A new execution context is created in the subcall with `x = 2, n = 1`
- Check `condition: n == 1` (which is now truthy)
- Returns `2`
- Removes this execution context from the memory/stack and resume previous execution context from paused point
- Returns `4`
- Removes this execution context from the memory/stack and resume previous execution context from paused point
- Returns `8`


## What are the benefits?

### 1. Recursive functions are easy to maintain
- Code is often short, simple, and therefore easier to maintain 
- You can clean them up by using a ternery statement to futher simplify it!

### 2. Recursive functions don't require us to track state with variables
- In an iterative loop, we typically use local variables to set and and track state. This has the danger of producing side effects.
- Recursive functions allow us to avoid using variables to manage state. Therefore there is no danger of side effects.
- Recursive exitucily and cleanly when they get to their terminal case. Since the function tests the value of the argument being passed in to the function immediately, before it does any further calculations, it reduces the chances of side effects.

Example of iterative approach that modifies state of variables:
```
var factor = function(number) {
  var result = 1;
  var count;
  for (count = number; count > 1; count--) {
    result *= count;
  }
  return result;
};
console.log(factor(6)); // 720
```

Example of recursive approach that does not need to modify state with variables:
```
var factorial = function(number) {
  if (number <= 0) {
    return 1;
  } else {
    return (number * factorial(number - 1));
  }
};
console.log(factorial(6)); // 720
```


### 3. Recursive traversal
- Iterating over data structures with nested data might require you to write nested subloops, which can get messy really quickly
- Recursion allows you to split similar tasks into smaller subtasks while keeping the code simple and easy to maintain
- An example of this is traversing a tree structure (like the DOM). It is much cleaner to use a recursive function than nesting iterative loops.

## What are the drawbacks?

### 1. Recursive functions require memory
- Iterative functions like loops usually require less memory since they execute in a single execution context. Their memory requirements are small and fixed.
- Recursive functions require multiple execution contexts which requires more memory.

### 2. Recursive functions can be called indefinitely
- Like iterative loops, a recursive function without an `if...else` statement or some sort of "setter" condition can end up calling itself indefinitely.
- The JavaScript engine will limit the maximal recursion depth (how many times the function can call itself). It is usually ~10,000 (give or take). 

## When should we use it? 
- When memory isn't an issue and a re-write as a loop only has trivial optimizations
- When a task can be split into several small tasks of the same kind with different parameters
- When working with nested data or non-linear data structures
- 

## If possible show an example of its use in a Shopify marketing repository.


## Resources
- https://javascript.info/recursion
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions#recursion
- https://www.programiz.com/javascript/recursion
- https://www.sitepoint.com/recursion-functional-javascript/
