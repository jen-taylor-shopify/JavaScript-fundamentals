# Scope and Closures

## Scope

### Remember
- **State**: The variables used to store and retrieve values (creates _state_)
- **Scope**: The set of rules that dictates how we store variables and find those variables at a later time (called _scope_) 
- JavaScript is a function-oriented language which means you can create a function at any moment!
	- Functions can access "outer variables" (variables outside of the function)

### Code Blocks
- If a variable is declared inside a code block `{...}`, its only visible inside that block 
```
{
  // do some job with local variables that should not be seen outside

  let message = "Hello"; // only visible in this block

  alert(message); // Hello
}

alert(message); // Error: message is not defined	
```
- We use this to isolate a piece of code that performs a specific task, with variables that belong only to that task.
- Variables declared in an `if`, `for`, or `while` block are only visible inside that block.
```
if (true) {
  let phrase = "Hello!";

  alert(phrase); // Hello!
}

alert(phrase); // Error, no such variable!
```

## Nested Functions
- Sometimes we write functions inside another function.
- Nested functions can access "outer variables" from the parent function.
- A nested function can be returned as a **property of a new object** or as **a result by itself**

But what happens when...
- You have a nested function like the one below? 
- If we create multiple counters, will they be independent? 
- What’s going on with the variables here?

```
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1
alert( counter() ); // 2
```

## Lexical Environment

### Step 1. Variables

- Every running function, code block, and script asa whole has an object known as the _lexical environment_.

- The **Lexical Environment** consists of two parts: 
	- **Environment Record** - an object that stores all the local variables as its properties (and other info like the value of `this`)
	-	Reference to the **outer lexical environment** - a reference to the outer code

- A **variable** is a property of the **Environment Record**
	- When you change a variable, you really change a property of that environment record

- **Global Lexical Environment** - is associated with the entire script. 
	- The reference to its **outer lexical environment** will be `null` because it is already the outermost environment.

- The **global lexical environment** changes during the execution of the code
	- Which means the properties of the **environment record** object change as the JS engine works through the code because some of the properties aren't immediately available. 
	- For example: 
	```
		let phrase;
		phrase = "hello"
	```
1. `phrase: uninitialized`: When the scrip starts, the lexical environment is pre-populated with all of the variables, but they are in an `uninitialized` state. The engine knows about the variable but it hasn't been declared yet. 
2. `let phrase`: Then the `let` definition appears. There is no assignment yet, so it is now `undefined`.
3. `phrase = "hello";`: Now the phrase has an assigned value of the string `"hello"`.

**A few reminders**
- The lexical environment is a **"specification object"** which means it only exists only "theoretically" (We can't actually call the object in our code and manipulate it directly).
- JavsScript engines may optimize the lexical environment and discard variables (properties) that are unused to save memory and perform other tasks.

### Step 2: Function Declarations

- A function is also a value (like a variable)
- BUT a function declaration is **instantly fully initialized** 

- When a lexical environment is created, a **Function Declaration is immediately ready to use**
```
let phrase = "hello";

function say(name) {
	alert(`$(phrase), $(name)`);
}
```
1. `phrase: uninitialized, say: function`: When the script starts the lexical environment saves the variable `phrase` in an `uninitialized` state. The function, however, is saved immeidately.

- What happens if the function is assigned to a variable as a **Function Expression**? 
	- Function Expressions behave like variables, and **will not** be instantly fully initialized

### Step 3: Inner and Outer Lexical Environment
- When a function runs, a new lexical environment is created.
- Like the global lexical environment, this new lexical environment is an object where variables are stored as properties.

**Example**
```
let phrase = "hello";

function say(name) {
	alert(`$(phrase), $(name)`);
}

say("Jen");
```
1. The **outer lexical environment** is created with the `say: function`, and `phrase: "hello"` properties.
2. The **inner lexical environment** is related to the _current_ execution of `say` and  has the `name` property. The value of `name` becomes `John`. It also has a reference to the **outer lexical environment**. 

In the example above, the code is looking for the `phrase` variable.
	- First it searches the **inner lexical environment**
	- If its not there, it searches the **outer lexical environment**, (and so on until it reaches the global one)
	- If the variable isn't found one of two things can happen: 
		- If you're in `use strict` mode an error will be triggered
		- If you're not in `use strict` mode, a new global variable will be created.

**🎨 Visual example:**
- An apartment building is the "**global lexical environment**". 
- It contains multiple "floors" which are the other "lexical environments".
- If you are searching for your friend, you start on the first floor (the inner most lexical environment), and keep going up a floor until you find them.

### Step 4: Returning a function

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
   - **Remember:**  the variable is alwasy updated in the **lexical environment** where it lives.

**🎨 Visual Example**
Think of each lexical context like a bubble. 
- Each new lexical scope is a new bubble inside the parent bubble.
- A bubble cannot overlap with another bubble, its either in or out. 

### What are the benefits of this lexical environment scope?

**1. You can write DRY by creating re-usable functions**
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

alert( counter2() ); // ?
alert( counter2() ); // ?
```
- `let counter` creates one lexical environment and `let counter2` creates a second lexical environment. They may call the same function, but they are different invocations so they are independent of one another.



### What are the drawbacks of this lexical environment scope?

**1. Technically you _can_ cheat the lexical scope**
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


## Closures

**Closure:** a function that remembers its outer variables and can access them. 
	- In JavaScript, all functions are naturally closures.
	- They automatically remember where they were created by storing this information in the environment property


## Garbage Collection
When a function finishes
- In most cases that that lexical environment and all variables are removed from memory to clear up space.
- If there's a nested function that is "still reachable" after the end of the function, then it will stay alive

**TL;DR** A lexical environment dies when it becomes unreachable. It only exists while there is a reference ot it. 

## Resources
- https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/scope%20%26%20closures/ch2.md
- 
