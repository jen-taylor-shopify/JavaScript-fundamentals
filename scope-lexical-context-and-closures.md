# Scope, Lexical Context, and Closures

- [Scope](#scope)
- [Lexical Context](#lexical_context)
- [Closures](#closures)

Before we start lets define a few terms and concepts: 
- **Variables**: Store values.
- **State**: The variables used to store and retrieve values creates _state_
- **Scope**: The set of rules that dictates _how_ we store variables and find those variables at a later time (called _scope_). 
- JavaScript is a **function-oriented language** which means you can create a function at any moment!
	- Functions can access "outer variables" (variables outside of the function)
---

## Scope

**TL;DR** In JavaScript, **scope** is the set of rules that dictates _how_ we store variables and find those variables at a later time.

### Scope in Code Blocks
- If a variable is declared inside a code block `{...}`, its only visible inside that block 
```
{
  let message = "Hello"; // only visible in this block

  alert(message); // Hello
}

alert(message); // Error: message is not defined	
```
- We use this to isolate a piece of code that performs a specific task, with variables that belong only to that task.
- Similarly, variables declared in an `if`, `for`, or `while` block are also only visible inside that block.

### Scope in Nested Functions
- Sometimes we write functions inside another function.
- Nested functions can access "outer variables" from the parent function.
- A nested function can be returned as a **property of a new object** or as **a result by itself** (we will clarify this below)
----

## Lexical Environment

To understand **scope** you need to understand the **lexical environment**. 
**TL;DR** Every running function, code block, and script asa whole has an object known as the _lexical environment_.

### What is the lexical environment?
The **Lexical Environment** consists of two parts: 
	1. **Environment Record** - an object that stores all the local variables as its properties (and other info like the value of `this`)
	2.	Reference to the **outer lexical environment** - a reference to the outer code

1. The **Environment Record**
	- A **variable** is a property of the environment record 
	- When you change a variable, you really change a _property of_ that environment record

2. The **Reference to the outer lexical environment**  
	- The **Global Lexical Environment** is the outermost lexical environment. It encompasses the entire script so it's reference to the outer lexical environment will be `null`.
	- When a lexical environment is nested inside the global lexical environment, its reference to the outer lexical environment will point to its parent lexical context

The **global lexical environment** changes during the execution of the code. 
	- This means the properties of the **environment record** object change as the JS engine works through the code because some of the properties aren't immediately available. 
	- This is a tricky concept so lets look at the following example to understand:
	```
		let phrase;
		phrase = "hello"
	```
	- When the script starts, the lexical environment is pre-populated with all of the variables but they are in an `uninitialized` state. The engine knows about the `phrase` variable but it hasn't been declared yet (`phrase: uninitialized`). 
	- Then the `let` definition appears. There is no assignment yet, so it is now `undefined`.
	- Next the assignment is evaluated and the JS engine understands that the string `hello` has been assigned to the `phrase` variable. 

The lexical environment is a **"specification object"** which means it only exists only "theoretically".
	- This means we can't actually call the object in our code and manipulate it directly.

Unused lexical environments and properties are "thrown out" because JavaScript has **garbage collection**.
	- JavsScript engines may optimize the lexical environment and discard variables (properties) that are unused to save memory and perform other tasks.

### Functions

**TL;DR** Functions are nuanced and have a few particularities that effect how they are treated in the lexical environment.

#### Function Declarations
- A function is also a value (like a variable) BUT a function declaration is **instantly fully initialized** 
- When a lexical environment is created, a **function declaration is immediately ready to use**
- Lets look at the following example to understand:
```
let phrase = "hello";

function say(name) {
	alert($(phrase), $(name)`);
}
```
	- When the script starts the lexical environment saves the variable `phrase` in an `uninitialized` state. The function, however, is saved immeidately (`phrase: uninitialized, say: function`).

#### Function Expressions
- If the function is assigned to a variable as a **Function Expression**, it behaaves exactly like a variable and **will not** be instantly fully initialized
- Lets look at the following example to understand:
```
let phrase = "hello";

let say = function(name) {
	alert($(phrase), $(name)`);
}
```
- When the script starts the lexical environment saves the variable `phrase` in an `uninitialized` state, and the variable `say` in the `uninitialized` state (`phrase: uninitialized, say: uninitialized`).

### Inner and Outer Lexical Environments
- When a function runs, a new lexical environment is created.
- Like the global lexical environment, this new lexical environment is an object where variables are stored as properties.

**Example 1**
```
let phrase = "hello";

function say(name) {
	alert(`$(phrase), $(name)`);
}

say("Jen");
```
1. The **outer lexical environment** is created with the `say: function`, and `phrase: "hello"` properties.
2. The **inner lexical environment** is related to the _current_ execution of `say` and  has the `name` property. The value of `name` becomes `John`. It also has a reference to the **outer lexical environment** (its parent block). 

In the example above, the code is looking for the `phrase` variable.
	- First it searches the **inner lexical environment**
	- If its not there, it searches the **outer lexical environment**, (and so on until it reaches the global one)
	- If the variable isn't found in any of the lexical environments one of two things can happen: 
		- If you're in `use strict` mode an error will be triggered
		- If you're not in `use strict` mode, a new global variable will be created.

**Example 2**

Lets use this example: 
```
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();

alert( counter() );
```

1. The **outer lexical environment** is created.
   - Properties `makeCounter: function` and `counter: undefined` created.
   - The "outer lexical environment" reference is null because this is the global lexical environment.
2. The **inner lexical environment** of the `makeCounter()` call is created to store variables.
   - Properties `count: 0` created
   - A tiny nested function is created for `return count++`, and it remembers that it was created inside this specific lexical environment.
   - The "outer lexical environment" reference  is the `global` lexical environment
3. ANOTHER **inner lexical environment** is created when `counter()` is called for the `return` function.
   - It's "outer lexical environment" reference is the `makeCounter()` lexical environment
4. When `counter()` is called, the `count++` looks inside its own lexical environment for a `count` variable. It doesn't exist there, so it goes up a level and searches the `makeCounter()` environment for a `count` variable. It exists and the function makes the change **where it finds the variable.**
   - **Remember:**  the variable is always updated in the **lexical environment** where it lives.

**🎨 Visual Example**
Think of each lexical context like a bubble. 
- Each new lexical scope is a new bubble inside the parent bubble.
- A bubble cannot overlap with another bubble, its either in or out. 

### Hoisting 
- JavaScript is not executed top-down, line-by-line. This has implications for the lexical environment _and_ scope.
- Lets look at an example to understand what this means:
```
a = 2;

var a;

console.log( a );
```
- Based on what we know about how the lexical environments work, we might expect the `console.log` in the previous example to return `undefined` since the declaration statement comes _after_ the assignment. 
- _Nope_ 👉 unlike other languages that are pre-compiled, the engine actually **compiles** our JavaScript code **right before** it interprets it
- part of this compilation phase involves finding and associating all declarations with their appropriate scopes
- all declarations, both variables and functions, are processed **first**, _before_ any part of the code is executed
  _SO_
	- during the **compilation phase** the JS engine sees the `var a` declaration
	- during the **execution phase** the JS sees the assignment `a = 2`

**🎨 Visual example**
- think of the JS engine "reorganizing" the code during the compilation process, moving it from where it appears in the code to the very top.
- Again, remember: **only the declarations** are hoisted. Assignments and executable logic is always left in place by the JS engine.

#### Function declarations vs. Function expresssions
- Remember: **only the declarations** are hoisted. Assignments and executable logic is always left in place by the JS engine.
- 
**Example 1: Function Declaration:**
```
foo();

function foo() {
	console.log( a ); // undefined

	var a = 2;
}
```
- the function call `foo()` still executes as expected because `function foo() {...}` is a declaration and is already _seen_ by the JS engine during the compilation process.


**Example 2: Function Expressions:**
Function expressions are NOT hoisted because they are an assignment. 
```
foo(); // TypeError!

var foo = function bar() {
	// ...
};
```
- the function call `foo()` is a TypeError because the declaration `var foo` is seen 👀 during the compilation phase, but the value is _not_ seen 👀 until the execution phase.
- `foo()` is attempting to invoke the undefined value, which is a `TypeError` illegal operation
- ALSO remember that because the name identifier `bar()` is on the RHS of this declaration, it is not available at the `var foo` scope! It could more accurately be depicted like this: 
```
foo = function() {
	var bar = ...self...
	// ...
}
```

#### Functions First
- functions and variable declarations are hosited.
- BUT **functions are hoisted FIRST**
	- Remember that subsequent function declarations do override previous ones though

#### Garbage Collection
- When a function finishes
	- In most cases that that lexical environment and all variables are removed from memory to clear up space.
	- If there's a nested function that is "still reachable" after the end of the function, then it will stay alive
- A lexical environment dies when it becomes unreachable. It only exists while there is a reference ot it.
---

## Closures

**TL;DR** Closure is when a function can remember _and_ access its lexical scope, even when its invoked outside its lexical scope

In JavaScript, all functions are naturally closures.
	- They are a natural result of writing code that relies on lexical scope
	- They automatically remember where they were created by storing this information in the **environment property**
	- They become important when you can recognize and leverage closures for your own will!

**Example 1: Same body function, different lexical environments**
Closures are powerful because they allow to functions to share the same body definition but store _different_ lexical environments.
```
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```
- In the code above both `add5` and `add10` are closures - they share the same body function ut they have completely seperate leical environments:
	- `add5`lexical environment: `x = 5`
	- `add10`lexical environment: `x = 10` 


Lets look at an example to dive deeper:
```
function foo() {
	var a = 2;

	function bar() {
		console.log( a ); // 2
	}

	bar();
}

foo();
```
- In the code above, "`function bar()` has a closure (remembers its parent) over the scope of `foo()`"
- This is because `bar()` is nested in `foo()`
- This is a very simple example so its hard to see what the real value of this is. So lets dive deeper.

**Example 2: Functions as a value let us acces lexical context _outside_ its lexical context**

Remember, every Closure has 3 scopes:
- Local Scope
- Outer Function Scope
- Global Scope

A common mistake is not realizing that in the case where the outer function is itself a nested function, access to the outer function's scope includes the enclosing scope of the outer function—effectively creating a chain of function scopes. To demonstrate, consider the following example code.

```
function foo() {
	var a = 2;

	function bar() {
		console.log( a );
	}

	return bar;
}

var baz = foo();

baz(); // 2 -- Whoa, closure was just observed!
```
- The function `bar()` has lexical scope access to its parent lexical environment, `foo()`
- But then we `return bar`, which means technically we are passing it _as_ a value. It returns the function object itself that `bar` references.
- Once `foo()` is executed, we assign the value it returned (the inner `bar()` function) to the variable `baz` (`var baz = foo()`)
- Then we invoke `baz()`, which invokes `foo()`, which invokes `bar()` (but by a _different_ reference) 
	- So `bar()` is executed from _outside_ its lexical context because invoking `baz()` >> invokes `foo()` >> which invokes `bar()` by `return bar`.

- Usually when the JS is done executing `foo()` the Garbage Collector would come along and free up memory by throwing away `foo()`'s inner scope.
	- **BUT** closures do not let this happen
	- WHY? The scope of `foo()` is still "in use" because the function `bar()` is still using it!
	- HOW? `bar()` has a lexical scope that references the inner scope of `foo()`. This needs to be kept alive for `bar()` to reference at a later time.
	- SO? The function is being invoked well outside of its author-time lexical scope. **Closure** lets the function continue to access the lexical scope it was defined in at author-time.

- 🤯 **TL;DR** any of the various ways that functions can be passed around as values and invoked in other locations, are examples of observing/exercising closure.




---

## Advantages

### Closure helps you keep code DRY by creating re-usable functions with different lexical scopes
- Because each invocation of a component creates a new lexical environment in JS, you can re-use functions without worrying about polluting the scope.
- If you have an instance like this: 
```
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();
let counter2 = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1

alert( counter2() ); // 0
alert( counter2() ); // 1
```
- `let counter` creates one lexical environment and `let counter2` creates a second lexical environment. They may call the same function, but they are different invocations so they are independent of one another.

### Lexical scope helps you avoid collisions
- Also supports "Principle of least priviledge" software design principle which states that you should expose only what is minimally necessary, and "hide" everything else. 
- If all variables were available in the global scope, they would be available to all nested scopes. But this can have unintended side effects. So its good practice to keep variables limited to the scopes that they are needed/used in.
- Hiding variables and functions inside a scope helps you avoid collisions between two different identifiers with the same name but different usages.
- (This often happens when you unexpectedly overwrite a value).
- For example, if you are using multiple libraries that don't have global "namespaces", collisions might occur if internal functions / variables have the same names as another library.

### Closures help us create "data encapsulation"
- By using a closure we can have private variables that are available even after a function task is finished.
- With a function closure we can store data in a separate scope, and share it only where necessary.

## When to leverage Closures
### Closures can be leveraged to keep variables and functions private
- Some programming languages allow you to declare private functions (that can only be called by other methods in the same class). JS does not!
- Closures help us emulate the concept of private methods. 
- Private methods are useful for managing the global namespace
- We can leverage the power of closures to create private variables and functions:
```
var counter = (function() {
  var privateCounter = 0;
  
	function changeBy(val) {
    privateCounter += val;
  }
  
	return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  };   
})();

counter.value(); // 0
counter.increment();
```
- `increment()`, `decrement()`, and `value()` are public functions
- `changeBy()` is a private function because it is not returned and only used internally. 
-  ？Question: `changeBy()` is not a "closure" because it isn't hoisted like `increment()`, `decrement()`, and `value()` and doesn't require the program to remember its parent lexical environment?????

- We can use closures to treat our functions like APIs that expose specific funcitons but also keep some of the logic internal and private 
- The following example uses the module design pattern, which uses closure to keep specific logic contained in a specific scope that is revealed only when its wanted:
```
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
- This is an example of three functions sharing the ame lexical environment.
- `CoolModule` **is a function** 
	- that it must be invoked to create a module instance
	- Without the execution of the outer function, the creation of the inner scope and the closures would not occur.
- `CoolModule()` **returns an object**
	- The object we return has references on it to the inner functions, but not to our inner data variables.
	- We keep the inner data variables hidden and private. 
	- Think of this object return value as essentially a public API for our module.
- `doSomething()` and `doAnother()` functions have closure over the inner scope of the `CoolModule()` "instance"
	-  When we transport those functions _outside_ of the lexical scope, by referencing them through the object (`foo.doSomething();`) we set up a condition where the lexical scope  by which closure can be observed and exercised.
- More simply, there are two "requirements" for the module pattern to be exercised:
	- There must be an outer enclosing function, and it must be invoked at least once (each time creates a new module instance).
	- The enclosing function must return back at least one inner function, so that this inner function has closure over the private scope, and can access and/or modify that private state.

## Disadvantages

### Technically you _can_ cheat the lexical scope
- You can use an `eval()` function (which takes a string as an argument) and cheat the lexical scope.

Example: 
```
function foo(str, a) {
	eval( str ); // cheating!
	console.log( a, b );
}

var b = 2;

foo( "var b = 3;", 1 ); // 1 3
```
- This strategy can be used to _dynamically_ modify the lexical scope.
- Its bad practice _and_ it negatively impacts performance because it subverts the JS engine's automatic performance optimizations

### Loops can get _loopy_
- Prior to the introduction of the `let` keyword, closures could create issues when inside a loop.
- If a `var` is declared inside the loop, it is hoisted, and therefore can create due to scoping.
- (Need to dig deeper into this)

### Duplicate definitions can occur
- duplicate definitions in the same scope are a really bad idea and will often lead to confusing results.
- Be careful about duplicate declarations, especially mixed between normal var declarations and function declarations -- peril awaits if you do!

### Closures prevent garbage collection
- While a closure is active, the memory cannot be garbage collected by the JS engine
- Its best practice not to unnecessarily create functions within other functions if closures are not needed for a particular task because it will negatively affect script performance both in terms of processing speed and memory consumption.
- This could lead to a memory "leak" (example?)
---

## Resources
- https://javascript.info/closure
- https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/scope%20%26%20closures/ch2.md
- https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/scope%20%26%20closures/ch4.md
- https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/scope%20%26%20closures/ch5.md
- https://medium.com/@deepakmankotiacse/what-is-closures-in-javascript-7acc626281ab
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures
