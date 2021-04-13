# Prototypes vs Classes in JavaScript 

**TL;DR:**
- 
- 

## Before there were `classes` there were `prototypes`
- JavaScript uses `prototypal inheritance`: every `object` inherits properties and methods from its `prototype object`.

### `[[Prototype]]`
- In JS, `objects` have a special hidden property, `[[Prototype]]` 
  - it is either `null`
  - or references another `object` (that object is called the `prototype`)
- If you read a property from the `object` that doesn't exist on the `object`, JS automatically looks to see if there is a `prototype` and if the `property` exists there. 

### `__proto__`
- `[[Prototype]]` is internal and hidden, but you can access it with the special name `__proto__`
```
let animal = {
  eats: true
};

let rabbit = {
  jumps: true
};

rabbit.__proto__ = animal; // sets rabbit.[[Prototype]] = animal

// we can find both properties in rabbit now:
alert( rabbit.eats ); // true (**)
alert( rabbit.jumps ); // true
```
- `animal` is the `prototype` of `rabbit` **OR** `rabbit` prototypically inherits from `animal`

- the prototypes form a chain:
```
let animal = {
  eats: true,
  walk() {
    alert("Animal walk");
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

let longEar = {
  earLength: 10,
  __proto__: rabbit
};

// walk is taken from the prototype chain
longEar.walk(); // Animal walk
alert(longEar.jumps); // true (from rabbit)
```
- `__proto__` is only used for reading properties. Any writing must be done directly on the object like this:
```
let animal = {
  eats: true,
  walk() {
    /* this method won't be used by rabbit */
    alert("Animal walk");
  }
};

let rabbit = {
  __proto__: animal
};

rabbit.walk = function() {
  alert("Rabbit bounce-bounce!");
};

rabbit.walk(); // Rabbit bounce-bounce!
```
- accessor properties work differently
  - `get` accessor: a function without arguments, that works when a property is read
  - `set` accessor: a function with one argument, that is called when the property is set
- So writing to a `set` property is actually the same as calling a function

```
let user = {
  name: "John",
  surname: "Smith",

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  },

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

let admin = {
  __proto__: user,
  isAdmin: true
};

alert(admin.fullName); // John Smith (*)

// setter triggers!
admin.fullName = "Alice Cooper"; // (**)

alert(admin.fullName); // Alice Cooper, state of admin modified
alert(user.fullName); // John Smith, state of user protected
```
- the value of `this` is not efected by `prototypes` at all
- **No matter where the method is found (in an `object` or its `prototype`), in a method call, `this` is always the object before the dot.**
  - This is important. If we have a big `object` with many methods and several `objects` that inherit from it, when the inheriting `objects` run the inherited methods, they will modify only their own states, not the state of the big `object`.
  - As a result, methods are shared, but the `object` state is not.

**Remember:**
- the value of `__proto__` can only be `null` or an `object` 
- `__proto__` is _not the same_ as `[[Prototype]]`
  - `__proto__` is a getter/setter for the `[[Prototype]]` property
  - `__proto__` is only for reading properties, **not writing properties**
- there can only be one `[[Prototype]]` (an `object` cannot inherit from two `[[Prototypes]]`)
- the value of `this` always refers to the corresponding object, not the prototype


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


- In JS, `class` inheritance is implemented on top of `prototypal inheritance`. You can write the same code in two different ways: 
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
### Classes
 -  `classes` are a template for creating objects
 -  they encapsulate data and code to work on that data

**Class Inheritance:**
- a class is like a blueprint - a description of the object to be created
- classes inherit from classes and create subclass relationships
- these have _hierarchical class taxonomies_
- a new instance of a `class` is instantiated via a constructor `function` with the `new` keyword

**Constructors**
- classes have a `constructor` method (there can only be one)
- JavaScript uses the **prototype chain** to wire the child `constructor.prototype` to the parent `constructor.prototype`
- `constructors` can use the `super` keyword to call the `constructor` of the super class
- This creates **single-ancestor parent/child hierarchies** and create the tightest coupling available in OO design.
- `constructors` can use the `super` keyword to call the `constructor` of the super class

**Methods**
Non-enumerable:
- `class` methods are non-enumerable
- A `class` definition sets enumerable flag to `false` for all methods in the "prototype" (if you were to `for..in` over an `object`, you usually don’t want its class methods).

Static:
- when the `static` keyword is placed before a method or property it means that method/property can't be called from the class instance
- `static` methods are used to create utility functions within the class
- `static` properties are used for caches or data that you don't want replicated across instances

**Instance Properties**
- properties that relate to the specific instance of the class must be defined inside of the class method

**Subclassing with `extends`**
- the `extends` keyword is used to create a `class` as a child of another `class`
- the subclass inherits from the parent `class`
- if there is a `constructor` in the subclass, it must call `super()` before using `this`



## Prototypes vs Classes: Ok so what's the difference?
- As we noted above, `class` inheritance is implemented on top of `prototypal inheritance`, **but that does not mean that it does the same thing**

### Instantiation and Inheritance
**PROTOTYPES:**
    - a **`prototype`** is an object instance.
    - A child of a prototype is another object instance which delegates to the parent any properties that aren’t implemented on the child.
    - A child of a prototype isn't a _copy_ of its prototype, its a living reference _to the prototype
    - Prototypes have flat inheritance structures.
**CLASSES:**
    - a **`class`** defines a _type_ which can be instatinated at runtime. 
    - A child of an ES6 class is another type definition which extends the parent with new properties and methods, which in turn can be instantiated at runtime.
    - Classes have hierarchical inheritance structures.
**WHY DO WE CARE?**
  - Because inheritance helps objects share code. 
  - The _way_ you share code has effects on the rest of the codebase architecture.
  - In JS  the terms "parent, child, ancestor, and descendant" do not have formal meanings like in other languages. Classes are a way of trying to emulate these formal relationships.
  - Class inheritance creates parent/child object taxonomies
    -  widespread use of a base class leads to the "fragile base class problem"
    -  it can be difficult to fix if you get them wrong

### Syntax
**PROTOTYPES**
- just gotta know that this secret property exists and how to use it
**CLASSES**
- their syntax creates a clear hierarchy/relationship for developers (example: `class Rabbit extends Animal`) 

**The key takeaways**: 
- `prototypes` don’t define a type; they are themselves instances and they’re mutable at runtime, with all that implies and entails.
- JavaScript can be a confusing mess and the addition of `classes` neither fixes it nor spares you having to understanding `prototypes`


### Advantages of using classes in JavaScript 
**Clear syntax for users**
  - class structure tells the user what is required for execution (of a function)

**Super class calls**
  - the `super` keyword can be used to call corresponding methods of the super `class` (i.e. `super.parentFunction()`)

### Disadvantages of using classes in JavaScript

**Class declarations aren't hoisted**
  - `function declarations` are hoisted, but `class declarations` are not
  - You must always declare your `class` **before** you access it

**Binding issues**
  - a class constructor requires the user to bind the `this` keyword in order to make it behave as expected 
  - this creates issues if you try to pass your class method as a callback

**Performance issues**
  - classes are more difficult for the JS Engine to optimize at runtime

**Strict hierarchies**
  - classes create a top-to-bottom order and changes are harder to implement after-the-fact

**React creators changed their minds**
  - The deprecated the class-based components. Will be removing them in the future.

## Conclusion: Maybe you should just be using a factory?

### Factories
Using a factory instead of a `prototype`or `class` syntax helps you:
- enforce incapsulation
- avoid inheritance issues
- you can destructure it because you don’t have to worry about the context of `this`
- allows you to use your objects interchangeably with vanilla JS and ES6 modules
- no binding or dealing with deeply nested properties
- lower memory and performance footprint in some cases

Example factory: 
```
function greeterFactory(greeting = "Hello", name = "World") {
  return {
    greet: () => `${greeting}, ${name}!`
  }
}

console.log(greeterFactory("Hey", "folks").greet()) // Hey, folks!
```

## SO?
Should you use a `class` or a `prototype` or a `factory`?
- which strategy will help you create the simplest solution?

## References
- https://medium.com/@vapurrmaid/should-you-use-classes-in-javascript-82f3b3df6195#:~:text=A%20class%20is%20a%20blueprint,as%20an%20object%20creation%20factory.
- https://everyday.codes/javascript/please-stop-using-classes-in-javascript/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes
- https://javascript.info/prototype-inheritance
- https://medium.com/@taylorshephard1/instantiation-patterns-in-javascript-7f9463b95839#:~:text=An%20instantiation%20pattern%20in%20JavaScript,Prototypical%2C%20Pseudoclassical%2C%20and%20ES6.
