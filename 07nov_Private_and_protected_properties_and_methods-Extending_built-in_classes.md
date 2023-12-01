# Private and protected properties and methods

One of the most important principles of object oriented programming – delimiting internal interface from the external one.
But in order to hide inner details, we’ll use not a protective cover, but rather special syntax of the language and conventions.

## Internal and external interface

In object-oriented programming, properties and methods are split into two groups:

- Internal interface – methods and properties, accessible from other methods of the class, but not from the outside.
- External interface – methods and properties, accessible also from outside the class.

An internal interface is used for the object to work, its details use each other.
Details are hidden and inaccessible. We can use its features via the external interface.

So, all we need to use an object is to know its external interface. We may be completely unaware how it works inside, and that’s great.

In JavaScript, there are two types of object fields (properties and methods):

- Public: accessible from anywhere. They comprise the external interface. Until now we were only using public properties and methods.
- Private: accessible only from inside the class. These are for the internal interface.

## Protecting “waterAmount”

Let’s make a simple coffee machine class first:

Eg:
class CoffeeMachine {
waterAmount = 0; // the amount of water inside

constructor(power) {
this.power = power;
alert( `Created a coffee-machine, power: ${power}` );
}

}

// create the coffee machine
let coffeeMachine = new CoffeeMachine(100);

// add water
coffeeMachine.waterAmount = 200;

Right now the properties waterAmount and power are public. We can easily get/set them from the outside to any value.

Let’s change waterAmount property to protected to have more control over it. For instance, we don’t want anyone to set it below zero.

Protected properties are usually prefixed with an underscore \_.

That is not enforced on the language level, but there’s a well-known convention between programmers that such properties and methods should not be accessed from the outside.

So our property will be called \_waterAmount:

Eg:
class CoffeeMachine {
\_waterAmount = 0;

set waterAmount(value) {
if (value < 0) {
value = 0;
}
this.\_waterAmount = value;
}

get waterAmount() {
return this.\_waterAmount;
}

constructor(power) {
this.\_power = power;
}

}

// create the coffee machine
let coffeeMachine = new CoffeeMachine(100);

// add water
coffeeMachine.waterAmount = -10; // \_waterAmount will become 0, not -10

Now the access is under control, so setting the water amount below zero becomes impossible.

## Read-only “power”

For power property, let’s make it read-only. It sometimes happens that a property must be set at creation time only, and then never modified.

That’s exactly the case for a coffee machine: power never changes.

To do so, we only need to make getter, but not the setter:

class CoffeeMachine {
// ...

constructor(power) {
this.\_power = power;
}

get power() {
return this.\_power;
}

}

// create the coffee machine
let coffeeMachine = new CoffeeMachine(100);

alert(`Power is: ${coffeeMachine.power}W`); // Power is: 100W

coffeeMachine.power = 25; // Error (no setter)

* Getter/setter functions

Here we used getter/setter syntax.

But most of the time get.../set... functions are preferred, like this:

class CoffeeMachine {
  _waterAmount = 0;

  setWaterAmount(value) {
    if (value < 0) value = 0;
    this._waterAmount = value;
  }

  getWaterAmount() {
    return this._waterAmount;
  }
}

new CoffeeMachine().setWaterAmount(100);

That looks a bit longer, but functions are more flexible. They can accept multiple arguments (even if we don’t need them right now).

On the other hand, get/set syntax is shorter, so ultimately there’s no strict rule, it’s up to you to decide.

* Protected fields are inherited

If we inherit class MegaMachine extends CoffeeMachine, then nothing prevents us from accessing this._waterAmount or this._power from the methods of the new class.

So protected fields are naturally inheritable. Unlike private ones that we’ll see below.

## Private “#waterLimit”

* This is a recent addition to the language. Not supported in JavaScript engines, or supported partially yet, requires polyfilling.

There’s a finished JavaScript proposal, almost in the standard, that provides language-level support for private properties and methods.

Privates should start with #. They are only accessible from inside the class.

For instance, here’s a private #waterLimit property and the water-checking private method #fixWaterAmount:

Eg:
class CoffeeMachine {
  #waterLimit = 200;

  #fixWaterAmount(value) {
    if (value < 0) return 0;
    if (value > this.#waterLimit) return this.#waterLimit;
  }

  setWaterAmount(value) {
    this.#waterLimit = this.#fixWaterAmount(value);
  }

}

let coffeeMachine = new CoffeeMachine();

// can't access privates from outside of the class
coffeeMachine.#fixWaterAmount(123); // Error
coffeeMachine.#waterLimit = 1000; // Error

On the language level, # is a special sign that the field is private. We can’t access it from outside or from inheriting classes.

Private fields do not conflict with public ones. We can have both private #waterAmount and public waterAmount fields at the same time.
For instance, let’s make waterAmount an accessor for #waterAmount:

class CoffeeMachine {

  #waterAmount = 0;

  get waterAmount() {
    return this.#waterAmount;
  }

  set waterAmount(value) {
    if (value < 0) value = 0;
    this.#waterAmount = value;
  }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); // Error

Unlike protected ones, private fields are enforced by the language itself. That’s a good thing.

But if we inherit from CoffeeMachine, then we’ll have no direct access to #waterAmount. We’ll need to rely on waterAmount getter/setter:

class MegaCoffeeMachine extends CoffeeMachine {
  method() {
    alert( this.#waterAmount ); // Error: can only access from CoffeeMachine
  }
}

In many scenarios such limitation is too severe. If we extend a CoffeeMachine, we may have legitimate reasons to access its internals. That’s why protected fields are used more often, even though they are not supported by the language syntax.

* Private fields are not available as this[name]

Private fields are special.

As we know, usually we can access fields using this[name]:
class User {
  ...
  sayHi() {
    let fieldName = "name";
    alert(`Hello, ${this[fieldName]}`);
  }
}

With private fields that’s impossible: this['#name'] doesn’t work. That’s a syntax limitation to ensure privacy.

## Summary

In terms of OOP, delimiting of the internal interface from the external one is called encapsulation.
It gives the following benefits:

Protection for users, so that they don’t shoot themselves in the foot

Imagine, there’s a team of developers using a coffee machine. It was made by the “Best CoffeeMachine” company, and works fine, but a protective cover was removed. So the internal interface is exposed.

All developers are civilized – they use the coffee machine as intended. But one of them, John, decided that he’s the smartest one, and made some tweaks in the coffee machine internals. So the coffee machine failed two days later.

That’s surely not John’s fault, but rather the person who removed the protective cover and let John do his manipulations.

The same in programming. If a user of a class will change things not intended to be changed from the outside – the consequences are unpredictable.

Supportable
The situation in programming is more complex than with a real-life coffee machine, because we don’t just buy it once. The code constantly undergoes development and improvement.

If we strictly delimit the internal interface, then the developer of the class can freely change its internal properties and methods, even without informing the users.

If you’re a developer of such class, it’s great to know that private methods can be safely renamed, their parameters can be changed, and even removed, because no external code depends on them.

For users, when a new version comes out, it may be a total overhaul internally, but still simple to upgrade if the external interface is the same.

Hiding complexity
eople adore using things that are simple. At least from outside. What’s inside is a different thing.

Programmers are not an exception.

It’s always convenient when implementation details are hidden, and a simple, well-documented external interface is available.
To hide an internal interface we use either protected or private properties:

1) Protected fields start with _. That’s a well-known convention, not enforced at the language level. Programmers should only access a field starting with _ from its class and classes inheriting from it.
2) Private fields start with #. JavaScript makes sure we can only access those from inside the class.

Right now, private fields are not well-supported among browsers, but can be polyfilled.

# Extending built-in classes

Built-in classes like Array, Map and others are extendable also.

For instance, here PowerArray inherits from the native Array:
// add one more method to it (can do more)
class PowerArray extends Array {
  isEmpty() {
    return this.length === 0;
  }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty()); // false

let filteredArr = arr.filter(item => item >= 10);
alert(filteredArr); // 10, 50
alert(filteredArr.isEmpty()); // false

Please note a very interesting thing. Built-in methods like filter, map and others – return new objects of exactly the inherited type PowerArray. Their internal implementation uses the object’s constructor property for that.

In the example above,

arr.constructor === PowerArray

When arr.filter() is called, it internally creates the new array of results using exactly arr.constructor, not basic Array. That’s actually very cool, because we can keep using PowerArray methods further on the result.
Even more, we can customize that behavior.

We can add a special static getter Symbol.species to the class. If it exists, it should return the constructor that JavaScript will use internally to create new entities in map, filter and so on.

If we’d like built-in methods like map or filter to return regular arrays, we can return Array in Symbol.species, like here:

class PowerArray extends Array {
  isEmpty() {
    return this.length === 0;
  }

  // built-in methods will use this as the constructor
  static get [Symbol.species]() {
    return Array;
  }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty()); // false

// filter creates new array using arr.constructor[Symbol.species] as constructor
let filteredArr = arr.filter(item => item >= 10);

// filteredArr is not PowerArray, but Array
alert(filteredArr.isEmpty()); // Error: filteredArr.isEmpty is not a function

As you can see, now .filter returns Array. So the extended functionality is not passed any further.

Other collections, such as Map and Set, work alike. They also use Symbol.species.

## No static inheritance in built-ins

Built-in objects have their own static methods, for instance Object.keys, Array.isArray etc.

As we already know, native classes extend each other. For instance, Array extends Object.
Normally, when one class extends another, both static and non-static methods are inherited. 

But built-in classes are an exception. They don’t inherit statics from each other.

For example, both Array and Date inherit from Object, so their instances have methods from Object.prototype. But Array.[[Prototype]] does not reference Object, so there’s no, for instance, Array.keys() (or Date.keys()) static method.

Here’s the picture structure for Date and Object:

![user reference](images/object-date-inheritance.svg "reference")
As you can see, there’s no link between Date and Object. They are independent, only Date.prototype inherits from Object.prototype.

That’s an important difference of inheritance between built-in objects compared to what we get with extends.