# Garbage collection and memory management

**Garbage collection:**
- JavaScript automatically allocates memory when objects are created, and frees it when they are not used anymore.
- Danger: It can give devs the false impression that they don't need to worry about memory management.

## What is the memory life cycle?

The memory lifecycle:
1. Allocate the memory you need
2. Use the allocated memory (read/write)
3. Release the allocated memory when it isn't needed anymore.

### Step 1: Allocate the memory you need

JavaScript automaticay allocates memory when values are initially declared.

Values:
```
var number = 123; // allocates memory for a number
var string = 'hello world'; // allocates memory for a string
```

Object: 
```
var object = {
  a: 1,
  b: 2,
  c: null,
}; // allocates memory for an object and contained values

```

Array:
```
var array = [1, null, 'hello again']; // (like object) allocates memory for the array and contained values
```

Function: 
```
function greeting(name) {
  return "Hello `name`";
} // allocates a function (which is a callable object)
```

Function Expressions:
```
button.addEventListener('click', function() {
  someElement.style.backgroundColor = 'blue';
}, false); // function expressions also allocate an object

```

Function calls:
```
var date = new Date(); // allocates a Date object
var element = document.createElement('div'); // allocates a DOM element
```

### Step 2: Use the allocated memory (read/write)

- At this step the engine "uses" the allocated values
- This basically means the engine is reading and writing in allocated memory. 
- This can be done by reading or writing the value of a variable or an object property or even passing an argument to a function.


### Step 3:  Release the allocated memory when it isn't needed anymore

This is where the majority of the memory management occurs.

In **low-level languages** the developer needs to manually determine when allocated memory is no longer needed and release it.

But JavaScript is a **high-level language**, and uses automatic memory management known as "garbage collection".
It invisibly takes care of memory behind the scenes for us.
- the garbage collector monitors memory allocation and determines when a block of allocated memory is no longer needed. Then it reclaims it! 
- important detail: this process is an approximation because "determining whether or not a specific piece of memory is still needed is undecidable" 
  - WTF does "undecidable" mean? -> "impossible to construct an algorithm that always leads to a correct yes-or-no answer"

## Garbage Collection
- the hardest part of garbage collection is automatically finding whether some memory is "not needed anymore"
- Because this is "undecidable", garbage collectors implement algorithms with restrictions

### References
- The main concept that the garbage collection algorithms rely on is the concept of **reference**
- An object **references** another object when it has access to the former object either implicitly or explicitly.
- **Example:** a JavaScript object has a reference to its prototype (implicit reference) and its properties values (explicit reference)
- An object is therefore broader than just a "JavaScript object" because it also contains function scopes (or the global lexical context)

### Algorithms

#### Reference counting algorithm
This is the most naive garbage collection algorithm.
- it simply decides if the object is still needed by checking to see if any other objects reference it
- If there are zero references to the object, it becomes garbage

Example:
```
var x = {
  a: {
    b: 2
  }
};
// 2 objects are created. One is referenced by the other as one of its properties.
// The other is referenced by virtue of being assigned to the 'x' variable.


var y = x;      // The 'y' variable is the second thing that has a reference to the object.

x = 1;          // Now, the object that was originally in 'x' has a unique reference
                //   embodied by the 'y' variable.

var z = y.a;    // Reference to 'a' property of the object.
                //   This object now has 2 references: one as a property,
                //   the other as the 'z' variable.

y = 'mozilla';  // The object that was originally in 'x' has now zero
                //   references to it. It can be garbage-collected.
                //   However its 'a' property is still referenced by
                //   the 'z' variable, so it cannot be freed.

z = null;       // The 'a' property of the object originally in x
                //   has zero references to it. It can be garbage collected.
```

**Limitation of this algorithm: Circular References**
- For example, if two objects are created with properties that reference one another, they create a cycle.
- Even though they are out of scope after the function call has completed, their memory can't be reclaimed because according to this algorithm, each object still has at least one reference pointing to it. 
- Now they can't be marked for garbage collection!
- This is a common cause of memory leaks.

Example:
```
function f() {
  var x = {};
  var y = {};
  x.a = y;        // x references y
  y.a = x;        // y references x

  return 'azerty';
}

f();
```

#### Mark-and-sweep algorithm / "Reachability"

As of 2012 all modern browsers use the concept of "reachability" to manage memory via garbage collection.
In this algorithm "an object is no longer needed" is defined as "an object is unreachable".
- It decides if the object is still needed by checking to see if it is "unreachable".

This algorithm assumes the knowledge of a set of objects called _roots_
- in JavaScript the root is the global object
- periodically the garbage collector looks at the rots and "marks" (or remembers) them
- the garbage collector starts at the root and finds all objects that are referenced from the roots, and objects referenced by those objects, on and on
- All visited objects are remembered so as not to visit the same object twice in the future
- This process is repeated until all reachable (from the roots) references are visited 
- All objects except the ones marked _reachable_ are deleted

Optimizations
- JavaScript engines apply many optimizations to make it run faster and not affect the execution
- **Generational collection** - objects are split into "new ones" and "old ones". Many objects do their job and die fast, and can be cleaned up aggressively. Those that survive for a longer period of time are marked as "old" and are examined less often by the garbage collector.
- **Incremental collection** - if there are many objects, walking and marking the whole object set at once might take time and create delays. So the engine splits the garbage collection into pieces which are executed one-by-one. It make take extra "bookkeeping" to track the changes, but it results in lots of tiny delays instead of one big delay.
- **Idle-time collection** - the garbage collector tries to run only while the CPU is idle to reduce effects on execution.

This is an improvement over the **reference counting** algorithm because: 
- We are sorting based on its reachability from the global context, not whether it has references or not, which is a better indicator of use. 
  - A set of objects might reference each other (which would cause the previous algorithm to save them in memory). But if the objects themselves aren't _reachable_ from the root, then there isn't any point in saving them in memory.
  - In the example above after the function call returns, the two objects are no longer referenced by any resource that is reachable from the **global object**. 
  - Therefore, they will be found unreachable by the garbage collector and have their allocated memory reclaimed.

**Limitation of this algorithm: Releasing memory manually**
- In order to release the memory of an object, it needs to be _explicitly_ unreachable
- it is not possible to explicitly trigger garbage collection in JS (yet)

## TL;DR

- Garbage collection is performed automatically in JS
- objects are retained in memory while they are "reachable" from the root
- being "referenced" is not the same as being "reachable" (i.e. a pack of interlinked objects can become unreachable)


## Resources

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management
- https://en.wikipedia.org/wiki/Undecidable_problem 
- https://javascript.info/garbage-collection
