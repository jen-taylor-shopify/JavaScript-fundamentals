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
	- Which means the properties of the **environment record** object change as the JS engine works through the code. 
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

- When a lexical environment 
