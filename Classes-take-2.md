# Classes in JavaScript 

**TL;DR: **
  -  `classes` are a template for creating objects
  -  they encapsulate data and code to work on that data


## Before there were classes there were prototypes
- JavaScript uses `prototypal inheritance`: every `object` inherits properties and methods from its `prototype object`.

**Prototype Inheritance:**
- a `prototype` is a working object instance. Objects inherit directly from other objects
- An instance can be composed of many different source objects (you can take a property from one place, a method from another, etc etc)
- This creates a flat **delegation hierarchy**


**TL;DR**
- Prototypes are loosy goosy and just go with the flow maaaaan.

## Classes are added to ES6 
- Unlike most other languages, JavaScript’s object system is based on `prototypes`, not `classes`.
- Although JavaScript is not an object-oriented language, aspects of object oriented programming (like the concept of `classes`) have been introduced to JS
- To bring the traditional `classes` to JavaScript, ES2015 standard introduced the `class` syntax
  - This is slightly misleading for developers coming from languages like Java though, because in JS `classes` don't come with any of the guarantees that a `class` was designed to provide in those OOP languages
- The introduction of `class` syntax into a language based on `prototypes` means that developers need to clearly understand `prototypes` and `classes` to use each pattern effectively.


In JavaScript, `class` inheritance is implemented on top of `prototypal inheritance`, **but that does not mean that it does the same thing**

**Class Inheritance:**
- a class is like a blueprint - a description of the object to be created
- classes inherit from classes and create subclass relationships
- these have _hierarchical class taxonomies_
- a new instance of a `class` is instantiated via a constructor `function` with the `new` keyword

**Constructors:**
- classes have a `constructor` method (there can only be one)
- JavaScript uses the **prototype chain** to wire the child `constructor.prototype` to the parent `constructor.prototype`
- `constructors` can use the `super` keyword to call the `constructor` of the super class
- This creates **single-ancestor parent/child hierarchies** and create the tightest coupling available in OO design.

**TL;DR**
- In JS  the terms "parent, child, ancestor, and descendant" do not have formal meanings like in other languages. Classes are a way of trying to emulate these formal relationships.




- JavaScript uses `prototypal inheritance`: every `object` inherits properties and methods from its `prototype object`.
- JS `classes` are built on top of the `prototypical inheritance`
- You can now write the same code in two different ways: 

**Prototype:**
```
function GreetFriend(greeting = "Hello", name = "World") {
  this.greeting = greeting
  this.name = name
}

GreetFriend.prototype.greet = function() {
  return `${this.greeting}, ${this.name}!`
}

const greetFriendProto = new GreetFriend("Hey", "folks")
console.log(greetFriendProto.greet())
```

**Classes:**
```
class GreetFriend {
  constructor(greeting = "Hello", name = "World") {
    this.greeting = greeting
    this.name = name
  }

  greet() {
    return `${this.greeting}, ${this.name}!`
  }
}

const greetFriend = new GreetFriend("Hey", "folks")

console.log(greetFriend.greet())
```


**Prototypes vs Classes**

**Instantiation**
- a **`class`** defines a _type_ which can be instatinated at runtime
- a **`prototype`** is an object instance. Instances are typically instantiated via factory functions, object literals, or `Object.create()`.

**Inheritance**
- Prototypes have flat inheritance structures.
- Classes have hierarchical inheritance structures.
- Why do we care? Because inheritance helps objects share code. The _way_ you share code has effects on the rest of the codebase architecture.
- Class inheritance creates parent/child object taxonomies
  -  widespread use of a base class leads to the "fragile base class problem"
  -  it can be difficult to fix if you get them wrong


Children:
- a child of an ES6 `class` is another _type_ definition which extends the parent with new properties and methods (and are also instantiated at runtime). 
- A child of a `prototype` is another object _instance_ which delegates any properties to the parent that aren't implemented on the child.

### Advantages of using classes in JavaScript 
**Clear syntax for users**
  - class structure tells the user what is required for execution (of a function)

**Super class calls**
  - the `super` keyword can be used to call corresponding methods of the super `class` (i.e. `super.parentFunction()`)

### Disadvantages of using classes in JavaScript

**Class declarations aren't hoisted**
  - `function declarations` are hoisted, but `class declatations` are not
  - You must always declare your `class` **before** you access it

Binding issues
  - a class constructor requires the user to bind the `this` keyword in order to make it behave as expected 
  - this creates issues if you try to pass your class method as a callback

Performance issues 
  - classes are more difficult for the JS Engine to optimize at runtime
  - 
Private variables
  - private variables are non-existent in JS (which is one of the primary advantages of using classes in other programming langauges)

4. Strict hierarchies
  - classes create a top-to-bottomorder and changes are harder to implement

5. React creators changed their minds.
  - The deprecated the class-based components. Will be removing them in the future.






## Object Oriented Programming (OOP)
- C++ and Java were developed around the OOP concept
- But the concept classes appears in many languages

### Why was OOP created? What problems do classes solve?
- reduces the complexity of software development by telling user what is required for execution (of a function)
- translates a program's structure into an understandable syntax for users


**Constructor**
- classes have a `constructor` method
- there can only be one `constructor` method
- `constructors` can use the `super` keyword to call the `constructor` of the super class

**Static methods**
- when the `static` keyword is placed before a method or property it means that method/property can't be called from the class instance
- `static` methods are used to create utility functions within the class
- `static` properties are used for caches or data that you don't want replicated across instances

**Instance Properties**
- properties that relate to the specific instance of the class must be defined inside of the class method

**Subclassing with `extends`**
- the `extends` keyword is used to create a `class` as a child of another `class`
- the subclass inherits from the parent `class`
- if there is a `constructor` in the subclass, it must call `super()` before using `this`






These two functions are the same, but one uses `classes` and one uses `prototypes`: 


What is the difference between the two? 
- The value of `this` behaves differently in each case.
- In the `class` example, you could use `this.bind` to make sure you always get the expected `this` value. (But should we have to fight with our tools?)

## Classes as Factories
A class is a blueprint for creating objects. 
- you can define properties and methods an object should have
- you can then make many objects with the same "blueprint"
- its like an object creation factory










## References
- https://medium.com/@vapurrmaid/should-you-use-classes-in-javascript-82f3b3df6195#:~:text=A%20class%20is%20a%20blueprint,as%20an%20object%20creation%20factory.
- https://everyday.codes/javascript/please-stop-using-classes-in-javascript/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes
