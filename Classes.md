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





