# Prototypes

_Prototypal inheritance_ is a language feature that allows us to take something and extend it. 

## `[[Prototype]]`

In JS, all objects have a hidden `[[Prototype]]` property
- `[[prototype]]` is internal and hidden
- It is either `null` or references another object (a `prototype`)
- When we read a property from an `object` and its missing, JavaScript automatially looks to see if the property exists on a `prototype`.
- It can be set by using the special name `_proto_` and we can use `obj._proto_` to access it

### Properties

```
let animal = {
  eats: true
};
let rabbit = {
  jumps: true
};

rabbit.__proto__ = animal; // sets rabbit.[[Prototype]] = animal
```
- in this example, if a property is missing from the `rabbit` object, it will then look for it on the `animal` object

```
// we can find both properties in rabbit now:
alert( rabbit.eats ); // true 
alert( rabbit.jumps ); // true
```
- `animal` is the prototype of `rabbit` 
- OR `rabbit` prototypically inherits from `animal`

### Methods

- if we havea method in the `animal` object, it can be called on `rabbit`

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

// walk is taken from the prototype
rabbit.walk(); // Animal walk
```

### Prototype chain

There can be several layers of prototypes:

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
- `animal` is the prototype for `rabbit`, `rabbit` is the prototype for `longEar`

Limitations: 
- Prototype chains can't go in circles. JS will throw an error if we try to make an assignment that forms a circle.
- The value of `__proto__` can be either an object or `null`. Other types are ignored.
- an object can only have one `[[Prototype]]`

## `_proto_`

`_proto_` is not the same as `[[Prototype]]`

`_proto_`: 
- is a getter/setter for the internal `[[Prototype]]` property
- it exists for historical reasons but is kind of outdated at this point 
- JS suggests you should use the `Object.getPrototypeOf` and `Object.setPrototypeOf` functions instead

## Get/Set vs Write/Delete on a prototype 

- prototype is only used for reading properties
- if you want to perform write/delete operations on the prototype you do it directly on the object

```
let animal = {
  eats: true,
  walk() {
    alert("Animal walks");
  }
};

let rabbit = {
  __proto__: animal
};

rabbit.walk = function() {
  alert("Rabbit! Bounce-bounce!");
};

rabbit.walk(); // Rabbit! Bounce-bounce!
```
- by re-defining it in the rabbit object, JS doesn't have to look up to the prototype for the function, and uses the instance on the `rabbit` object

## `this` in prototypes

- `this` is not affected by prototypes
- no matter where the method is found (in an object or in its prototype), in the method call `this` is always the object before the dot
- we may have a big object with many methods, and have objects that inherit from it -> When the inheriting objects run the inherited methods, they will modify only their own states, not the state of the big object.

```
// animal has methods
let animal = {
  walk() {
    if (!this.isSleeping) {
      alert(`I walk`);
    }
  },
  sleep() {
    this.isSleeping = true;
  }
};

let rabbit = {
  name: "White Rabbit",
  __proto__: animal
};

// modifies rabbit.isSleeping
rabbit.sleep();

alert(rabbit.isSleeping); // true
alert(animal.isSleeping); // undefined (no such property in the prototype)
```

**TL;DR** Methods are shared, but the object state is not.

## for...in loop

- A `for...in` loop iterates over the object's properties and its prototypically inherited properties
- If you want to see an object's methods (excluding the inherited ones) you can use the `obj.hasOwnProperty(key)` method which returns `true` if it has its own propety named `key` 

## Native prototypes

- the `prototype` property is widely used in the core of JS
- all built-in constructor functions use it

### Object.prototype
- `let obj = {};` has built-in methods like `toString`
