# Factory Functions

## Constructors

### What is a constructor? 
In classic object-oriented programming languages, a constructor is a special method used to initialize a newly created class object.
In ES6 JavaScript introduced the concept of `constructors`:
- Every JavaScript function has a `prototype` object, and the constructor leverages the `prototype` to mimic the behaviour of constructors in OOP languages
- we use the keyword `new` when calling a function which tells JavaScript we would like the function to behave like a constructor
- when we call `new` it 
  -  instantiates a new instance object and binds `this` to it within the constructor
  -  binds `instance.__prototype__` to `Constructor.prototype` (which binds `instance.__proto__.constructor` to `Constructor`
  -  returns `this` which refers to the new object instance

### The problem with constructors
Constructors are tricky because they _look_ like regular functions but they don't behave like a regular function. 
- it can't be used without the `new` keyword (and if you forget it, it often fails silently)
- inheritance can get tricky

## Factory Functions
The factory function is similar to a constructor
- BUT instead of using a `new` keyword to create an object, the factory function sets up and returns the new object when its called.

**The Constructor Pattern:**
```
const Person = function(name, age) {
  this.sayHello = () => console.log('hello!');
  this.name = name;
  this.age = age;
};

const jeff = new Person('jeff', 27);
```

**The Factory Pattern:**
```
const personFactory = (name, age) => {
  const sayHello = () => console.log('hello!');
  return { name, age, sayHello }; // ES6 introduced this shorthand - it is the same as `return {name: name, age: age, sayHello: sayHello}`
};

const jeff = personFactory('jeff', 27);

console.log(jeff.name); // 'jeff'

jeff.sayHello(); // calls the function and logs 'hello!'
```

The Factory Function pattern is a powerful way to organize and contain the code you write.
- Factories are just plain old JavaScript functions that return objects for us to use in our code
- We leverage scope, closures, private variables, and functions in our Factory Functions to create easy-to-debug code
- Creating objects through factories forces the developer to put thought into how and when we want to share specific information

## Scope, Closure, Private Variables, and Functions

Every time we create a new function, we introduce a new function **scope**.

In the example below, we see **scope** in action. 
Neither of the functions created _inside_ of the `FactoryFunction` can be accesssed outside of the function itself.

```
const FactoryFunction = string => {
  const capitalizeString = () => string.toUpperCase();
  const printString = () => console.log(`----${capitalizeString()}----`);
};

const taco = FactoryFunction('taco');

printString(); // ERROR!! We are outside the function scope!
capitalizeString(); // ERROR!! We are outside the function scope!
```

The only way we _could_ access them is if we `return` them in the object. 
Below, we only `return` the `printString` function, so we can only access `taco.printString` (and not `taco.capitalizeString`).
```
const FactoryFunction = string => {
  const capitalizeString = () => string.toUpperCase();
  const printString = () => console.log(`----${capitalizeString()}----`);
  return { printString };
};

const taco = FactoryFunction('taco');

taco.printString(); // this prints "----TACO----"
taco.capitalizeString(); // ERROR!! We don't have access to that function scope!
```
Even though we can't access the `capitalizeString()` function, the `printString()` function _can_.
This is because of the concept of **closure**.
- Remember: 
  - Functions retain their scope even if they are passed around and called outside of that scope. 
  - In the example `printString` has access to everything inside of `FactoryFunction`, even if it gets called outside of that function.

For another example, check out the [counter example](https://www.theodinproject.com/paths/full-stack-javascript/courses/javascript/lessons/factory-functions-and-the-module-pattern) in this blog post.

Its important to undersatnd closures because they allow us to create **private** variables and functions. 
- `capitalizeString()` is a private function. It is only meant to be used in the function and not in other parts of the program
- `printString()` is a public function. We expose it with the `return` so that the rest of the program can use this function.

Making functions inaccessible (private) is useful because: 
- it makes code easier to refactor 
- it makes code easier to test
- it reduces the change of data leakages

## How does this apply to the Factory Function Pattern? 

### Scope and Private Functions
```
const Player = (name, level) => {
  let health = level * 2;
  const getLevel = () => level;
  const getName  = () => name;
  const die = () => {
    // uh oh
  };
  const damage = x => {
    health -= x;
    if (health <= 0) {
      die();
    }
  };
  const attack = enemy => {
    if (level < enemy.getLevel()) {
      damage(1);
      console.log(`${enemy.getName()} has damaged ${name}`);
    }
    if (level >= enemy.getLevel()) {
      enemy.damage(1);
      console.log(`${name} has damaged ${enemy.getName()}`);
    }
  };
  return {attack, damage, getLevel, getName}
};

const jimmie = Player('jim', 10);
const badGuy = Player('jeff', 5);
jimmie.attack(badGuy);
jimmie.health = 0; // ERROR - this isn't a public method
```

In the example above we cannot manipulate Jimmie's health directly. 
- Jimmie's health is a private variable inside of the object. 
- Jimmie's health has NOT been exposed publicy so we get an error when we try to call it outside the scope of that function.
- this means that all of the logic related to Jimmie's health is encapsulated in the jimmie instance
- this makes reasoning and debugging _much_ easier

### Inheritance 

Constructors leverage the `prototype` object on every function to give objects access to the methods and properties of another object.
We can leverage the same behaviour with factories!

The following example shows how we can
-  pick which functions we want to include in a new object
- 
```
const Person = (name) => {
  const sayName = () => console.log(`my name is ${name}`)
  return {sayName}
}

const Nerd = (name) => {
  // simply create a person and pull out the sayName function with destructuring assignment syntax!
  const {sayName} = Person(name)
  const doSomethingNerdy = () => console.log('nerd stuff')
  return {sayName, doSomethingNerdy}
}

const jeff = Nerd('jeff')

jeff.sayName() //my name is jeff
jeff.doSomethingNerdy() // nerd stuff
```

## Modules
ES6 not only introduced the concept of classes, but also the concept of `modules`

## Resources
- https://www.theodinproject.com/paths/full-stack-javascript/courses/javascript/lessons/factory-functions-and-the-module-pattern
- https://addyosmani.com/resources/essentialjsdesignpatterns/book/#constructorpatternjavascript
