# Recursion and Stack
--- 

## What is it?

### TL:DR;

- Recursion is a programming pattern that is not exclusive to Javscript.
- When a function solves a task, it can call other functions in the process. When a function calls _itself_, its called _recursion_.

### How does it work? 

There are two ways to accomplish the same task - iterative and recursive.

#### Iterative thinking

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

A `for` loop uses a loop algorithm to execute a `for ` statement part-by-part. 

The statement `for (let i = 0; i < 3; i++)` results in the following executions: 
- begin `i = 0`: this executes once when the loop is entered
- condition `i < n`: the `condition` is checked before every iteration of the loop. If it is false, the loop stops.
- body `result *= x`: the `body` runs each time the `condition` is truthy.
- step `i++` executes after the `body` on each iteration

Which step-by-step looks like this for `(pow 2, 3)`: 
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




#### Recursive thinking

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

Which step-by-step looks like this for `(pow 2, 3)`: 


The recursive method reduces a function call to a simpler function on each loop, until the result becomes obvious, and then – to even more simpler, and so on, until the result becomes obvious.






## What are the benefits? Or in what context would you want to use this?

Recursion is useful in the following situations: 
- when a task can be naturally split into several task of the same kind, but simpler
- when a task can be simplified into aneasy action plus a simpler variant of the same task
- when working with specific data structures

A recursive function always has a condition that stops the function from calling itself whereas a loop might 





## What are the drawbacks? Or in what contexts would you not want to use this?


## If applicable show an example of its use in Shopify marketing.


## Resources
- https://javascript.info/recursion
- 
