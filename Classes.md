# Classes

- When writing JS we often need to create many objects of the same kind (i.e. useres, goods, etc)
- The "class" construct has features that are useful for object-oriented programming

## The 'class" syntax

Basic syntax: 
```
class MyClass {
  constructor() {}
  method1() {...}
  method2() {...}
  method3() {...}
}
```

Extending:
- `new MyClass()` creates a new object with all of the listed methods 
- the `constructor()` method is alld automtically in the `new` instance so we can initialize the object there

## What is a class?

**TL;DR** A class is a kind of function

Example: 
```
class User {
  constructor(name) { 
    this.name = name;
  }
  
  sayHi() {
    alert(this.name);
  }
}

// proof: User is a function
alert(typeof User); // function
```
The example above:
- Uses a `class declaration` to create a function named `User`
- Stores class methods in `User.prototype`

The `new User` object now has access to the `class User` prototype!
- When a `new User` object is created, it looks to the `prototype` (`class User`) for the method.

## More than syntactic sugar

**1. The classConstructor property**
- A function created by a `class` is labelled by a special internal property `[[FunctionKind]]:"classConstructor"`
- Unlike regular functions, to access class functions, you must call instances with the `new` keyword

**2. Classes are non-enumerable**
- Class methods are non-enumerable (the `enumerable` flag is set to `false` for all methods in the `prototype`)
- This is a good feature because if you were to loop over an object with `for...in`, you wouldn't want to access the class methods

**3. Classes always use strict**
- All code inside the class construct is automaticaly in `strict` mode
- This means that if a variable can't be found in any of the lexical contexts, it will throw an error

## Class fields

"Class fields" is a syntax that allows us to add properties:
```
class User {
  name = "John";

  sayHi() {
    alert(`Hello, ${this.name}!`);
  }
}

new User().sayHi(); // Hello, John!
```

"Class fields" are set on _individual objects_, not `User.prototype`
```
class User {
  name = "John";
}

let user = new User();
alert(user.name); // John
alert(User.prototype.name); // undefined
```

## Binding `this`

Functions in JS have a dynamic `this`. It changes depending on the context of the call.
When an object method is passed around and called in another context, `this` won't be a reference to its object anymore. 

```
class Button {
  constructor(value) {
    this.value = value;
  }

  click() {
    alert(this.value);
  }
}

let button = new Button("hello");

setTimeout(button.click, 1000); // undefined
```

This is called "losing `this`" and we can solve it with Function binding:
- We can bind the method to the object (ie. we often do this in the constructor)
- Use a wrapper function `setTimeout(() => button.click(), 1000)` (??)

This ability to make sure that the value of `this` will always refer to the intended value is a feature of classes.
Example: 
```
class Button {
  constructor(value) {
    this.value = value;
  }
  click = () => {
    alert(this.value);
  }
}

let button = new Button("hello");

setTimeout(button.click, 1000); // hello
```
We can pass `button.click` around anywhere and despite the context the value of `this` will always be correct. 

## Class Inheritance

We can create new functionality on top of an existing class by _extending_ the class.

If you have the following `Animal` class:
```
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} stands still.`);
  }
}

let animal = new Animal("My animal");
```
- Any instance of `new Animal("My animal");` will look to the `Animal.prototype`
- If we created a new class called `Rabbit` that was based on `Animal`, it would have access to the `Animal` methods so that `Rabbits` get the functionality  "generic" animals, as well as their own unique functionality
- syntax: `class Rabbit extends Animal` 

```
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }
}

let rabbit = new Rabbit("White Rabbit");

rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.hide(); // White Rabbit hides!
```
For `rabbit.run` method, the JS engine checks 
- first the `rabbit` object (its not there)
- Then it goes up a level to check the prototype `Rabbit.prototype` (which has no run method)
- Then it looks to the `Animal.prototype` which it has access to because `Rabbit` extends `Animal`. It finds the `run` method!

## Overriding inherited methods

We can also override methods in the prototype. 
- By default, methods that are not specified in the child (`Rabbit`) are taken "as-is" from the Prototype `Animal`
- But we might want to specify our own method or build on top of the parent method
- The `super` keyword is used for this

- `super.method()` calls the parent method
- `super(...)` calls the parent constructor (inside the child constructor)

Example: 
```
// Animal stop method:
 stop() {
    this.speed = 0;
    alert(`${this.name} stands still.`);
  }

// Rabbit stop method with additional functionality:
 stop() {
    super.stop(); // call parent stop
    this.hide(); // and then hide
  }
```

**Note:**
- Arrow functions do not have `super` 

## Overriding constructor

The extended class `Rabbit` did not have its own constructor.
- if a class extends another class and has no constructor, then the following “empty” constructor is generated:
```
 constructor(...args) {
    super(...args);
  }
```

But we can also add a custom constructor to `Rabbit`
- in order for it to work you **MUST** call **super(...)** before calling `this`!
- when a contructor is in aninheriting class it gets a special internal property `[[ConstructorKind]]: "derived"`
- This is a special internal label and has effects when it encounters `new`:
  - When a regular function is executed with `new` it creates an empty object and assigns it to `this`
  - When a derived constructor runs, it _doesn't_ do this. It expects the parent constructor to make the object and assign `this`
  - So a derived constructor _needs_ to call `super` in order to execute its parent constructor, and create the object and assign `this`

Example: 
```
class Rabbit extends Animal {

  constructor(name, earLength) {
    super(name);
    this.earLength = earLength;
  }

  // ...
}
```

## Static properties and methods

We can assign a method to a class function itself (not to its "prototype") - these are called `static`
The syntax looks like this: 
```
class User {
  static staticMethod() {
    alert(this === User);
  }
}

User.staticMethod(); // true
```
Summary: 
- Static methods are used for the functionality that belongs to the class “as a whole”. It doesn’t relate to a concrete class instance.
- Static properties are used when we’d like to store class-level data, also not bound to an instance.
- Technically, static declaration is the same as assigning to the class itself


