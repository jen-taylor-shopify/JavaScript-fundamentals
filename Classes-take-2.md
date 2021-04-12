# Classes in JavaScript

## What is a class? 
- TL;DR: `classes` are a template for creating objects
- In langauges like Java or Swift, a `class` is "the blueprint to create objects"
- To bring the traditional classes to JavaScript, ES2015 standard introduces the class syntax: a syntactic sugar over the prototypal inheritance.
- They encapsulate data and code to work on that data

## Before there were classes there were prototypes
- JavaScript uses `prototypal inheritance`: every `object` inherits properties and methods from its `prototype object`.





## Classes are added to ES6
- JavaScript is not an object-oriented language, it was not designed to be one
- In JavaScript everything is an object.
- In JS an object is a Map data structure with some **sophisticated lookup procedures**
- To bring the traditional `classes` to JavaScript, ES2015 standard introduced the `class` syntax
- JS `classes` are built on top of the `prototypical inheritance`


Lets use the example of the classes `Car` and `SportsCar` (which inherits `Car`)
- They both have `make` and `model` properties, and a `start` method
- `Sportscar` has `turbocharge` property that overrides the `start` method

```
// 'class' declaration / constructor function
function Car(make, model) {
  this.make = make;
  this.model = model;
}

// the start method
Car.prototype.start = function() {
  console.log('vroom');
}

// inheritance example / constructor function 
function SportsCar(make, model, turbocharged) {
  Car.call(this, make, model);
  this.turbocharged = turbocharged;
}

// actual inheritance logic
SportsCar.prototype = Object.create(Car.prototype);
SportsCar.prototype.constructor = SportsCar;

// overriding the start method
SportsCar.prototype.start = function() {
  console.log('VROOOOM');
}

// Now testing the classes
const car = new Car('Nissan', 'Sunny');
car.start(); // vroom
console.log(car.make); // Nissan

const sportsCar = new SportsCar('Subaru', 'BRZ', true);
sportsCar.start(); // VROOOOM
console.log(car.turbocharged); // true

```

`car.start()` does the following:
- JS engine looks at the `car` object for a value with the key `start`
- The `Car` object doesn't have that value
- The JS engine asks the `car.prototype` object for a value with the key `start`
- The `car.prototype` returns the `start` function and the JS engine executes it (`console.log('vroom');`)

`sportscar.start()` does the following: 
- Firstly, the line `SportsCar.prototype = Object.create(Car.prototype);` returns a new object with the `prototype` set to whatever was passed in (in this case, the `car.prototype`)
- JS engine looks at the `SportsCar` object for a value with the key `start`
- The `SportsCar` object doesn't have that value
- The JS engine asks the `sportsCar.prototype` object for a value with the key `start`
- The `sportsCar.prototype` object doesn't have that value
- The JS engine asks the `sportsCar.prototype.prototype` (which is the `car` object) for a value with the key `start` 


## Object Oriented Programming (OOP)
- C++ and Java were developed around the OOP concept
- But the concept classes appears in many languages

### Why was OOP created? What problems do classes solve?
- reduces the complexity of software development by telling user what is required for execution (of a function)
- translates a program's structure into an understandable syntax for users

### Classes in JavaScript
- in es6 `classes` were added to JavaScript
- `classes` use `functions` and `prototypical inheritance` under the hood

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

**Prototypes**
```
function Person (name) {
  this.name = name
}
Person.prototype.talk = function () {
  console.log(`${this.name} says hello`)
}
```

**Classes:**
```
class Person {
  constructor (name) {
    this.name = name
  }
  
  talk () {
    console.log(`${this.name} says hello`)
  }
}
```

What is the difference between the two? 
- The value of `this` behaves differently in each case.
- In the `class` example, you could use `this.bind` to make sure you always get the expected `this` value. (But should we have to fight with our tools?)

## Classes as Factories
A class is a blueprint for creating objects. 
- you can define properties and methods an object should have
- you can then make many objects with the same "blueprint"
- its like an object creation factory

## Advantages of using classes in JavaScript 
**Clear syntax for users**
  - class structure tells the user what is required for execution (of a function)

**Super class calls**
  - the `super` keyword can be used to call corresponding methods of the super `class` (i.e. `super.parentFunction()`)

## Disadvantages of using classes in JavaScript

**Class declarations aren't hoisted**
  - `function declarations` are hoisted, but `class declatations` are not
  - You must always declare your `class` before you access it

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








## References
- https://medium.com/@vapurrmaid/should-you-use-classes-in-javascript-82f3b3df6195#:~:text=A%20class%20is%20a%20blueprint,as%20an%20object%20creation%20factory.
- https://everyday.codes/javascript/please-stop-using-classes-in-javascript/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes
