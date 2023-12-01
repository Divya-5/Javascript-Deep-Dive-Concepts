# Class checking: "instanceof"

The instanceof operator allows to check whether an object belongs to a certain class. It also takes inheritance into account.
Such a check may be necessary in many cases. For example, it can be used for building a polymorphic function, the one that treats arguments differently depending on their type.

## The instanceof operator

The syntax is:

obj instanceof Class

It returns true if obj belongs to the Class or a class inheriting from it.

For instance:
class Rabbit {}
let rabbit = new Rabbit();

// is it an object of Rabbit class?
alert( rabbit instanceof Rabbit ); // true

It also works with constructor functions:

// instead of class
function Rabbit() {}

alert( new Rabbit() instanceof Rabbit ); // true

…And with built-in classes like Array:

let arr = [1, 2, 3];
alert( arr instanceof Array ); // true
alert( arr instanceof Object ); // true

Please note that arr also belongs to the Object class. That’s because Array prototypically inherits from Object.

Normally, instanceof examines the prototype chain for the check. We can also set a custom logic in the static method Symbol.hasInstance.
The algorithm of obj instanceof Class works roughly as follows:

1. If there’s a static method Symbol.hasInstance, then just call it: Class[Symbol.hasInstance](obj). It should return either true or false, and we’re done. That’s how we can customize the behavior of instanceof.

For example:
// setup instanceOf check that assumes that
// anything with canEat property is an animal
class Animal {
static [Symbol.hasInstance](obj) {
if (obj.canEat) return true;
}
}

let obj = { canEat: true };

alert(obj instanceof Animal); // true: Animal[Symbol.hasInstance](obj) is called

2. Most classes do not have Symbol.hasInstance. In that case, the standard logic is used: obj instanceOf Class checks whether Class.prototype is equal to one of the prototypes in the obj prototype chain.
   In other words, compare one after another:
   obj.**proto** === Class.prototype?
   obj.**proto**.**proto** === Class.prototype?
   obj.**proto**.**proto**.**proto** === Class.prototype?
   ...
   // if any answer is true, return true
   // otherwise, if we reached the end of the chain, return false

In the example above rabbit.**proto** === Rabbit.prototype, so that gives the answer immediately.

In the case of an inheritance, the match will be at the second step:
class Animal {}
class Rabbit extends Animal {}

let rabbit = new Rabbit();
alert(rabbit instanceof Animal); // true

// rabbit.**proto** === Animal.prototype (no match)
// rabbit.**proto**.**proto** === Animal.prototype (match!)

Here’s the illustration of what rabbit instanceof Animal compares with Animal.prototype:
![user reference](images/instanceof.svg "reference")

By the way, there’s also a method objA.isPrototypeOf(objB), that returns true if objA is somewhere in the chain of prototypes for objB. So the test of obj instanceof Class can be rephrased as Class.prototype.isPrototypeOf(obj).

It’s funny, but the Class constructor itself does not participate in the check! Only the chain of prototypes and Class.prototype matters.

That can lead to interesting consequences when a prototype property is changed after the object is created.

Like here:

function Rabbit() {}
let rabbit = new Rabbit();

// changed the prototype
Rabbit.prototype = {};

// ...not a rabbit any more!
alert( rabbit instanceof Rabbit ); // false

Uncaught SyntaxError: Identifier 'Rabbit' has already been declared

## Bonus: Object.prototype.toString for the type

We already know that plain objects are converted to string as [object Object]:
let obj = {};

alert(obj); // [object Object]
alert(obj.toString()); // the same

That’s their implementation of toString. But there’s a hidden feature that makes toString actually much more powerful than that. We can use it as an extended typeof and an alternative for instanceof.

Sounds strange? Indeed. Let’s demystify.
By specification, the built-in toString can be extracted from the object and executed in the context of any other value. And its result depends on that value.
* For a number, it will be [object Number]
* For a boolean, it will be [object Boolean]
* For null: [object Null]
* For undefined: [object Undefined]
* For arrays: [object Array]

Let’s demonstrate:
// copy toString method into a variable for convenience
let objectToString = Object.prototype.toString;

// what type is this?
let arr = [];

alert( objectToString.call(arr) ); // [object Array]

Here we used call, call/apply to execute the function objectToString in the context this=arr.

Internally, the toString algorithm examines this and returns the corresponding result. More examples:

let s = Object.prototype.toString;

alert( s.call(123) ); // [object Number]
alert( s.call(null) ); // [object Null]
alert( s.call(alert) ); // [object Function]

## Symbol.toStringTag
The behavior of Object toString can be customized using a special object property Symbol.toStringTag.
let user = {
  [Symbol.toStringTag]: "User"
};

alert( {}.toString.call(user) ); // [object User]

For most environment-specific objects, there is such a property. Here are some browser specific examples:
// toStringTag for the environment-specific object and class:
alert( window[Symbol.toStringTag]); // Window
alert( XMLHttpRequest.prototype[Symbol.toStringTag] ); // XMLHttpRequest

alert( {}.toString.call(window) ); // [object Window]
alert( {}.toString.call(new XMLHttpRequest()) ); // [object XMLHttpRequest]

As you can see, the result is exactly Symbol.toStringTag (if exists), wrapped into [object ...].

At the end we have “typeof on steroids” that not only works for primitive data types, but also for built-in objects and even can be customized.

We can use {}.toString.call instead of instanceof for built-in objects when we want to get the type as a string rather than just to check.

### Summary
Let’s summarize the type-checking methods that we know:

* typeof works for primitives return string
* {}.toString works for primitives, built-in objects, objects with Symbol.toStringTag	returns string
* instanceof works for objects returns true/false

As we can see, {}.toString is technically a “more advanced” typeof.

And instanceof operator really shines when we are working with a class hierarchy and want to check for the class taking into account inheritance.


#### Tasks
## Strange instanceof
Q) In the code below, why does instanceof return true? We can easily see that a is not created by B().

function A() {}
function B() {}

A.prototype = B.prototype = {};

let a = new A();

alert( a instanceof B ); // true

Yeah, looks strange indeed.

But instanceof does not care about the function, but rather about its prototype, that it matches against the prototype chain.

And here a.__proto__ == B.prototype, so instanceof returns true.

So, by the logic of instanceof, the prototype actually defines the type, not the constructor function.

![user reference](images/mixin-inheritance.svg "reference")

That’s because methods sayHi and sayBye were initially created in sayHiMixin. So even though they got copied, their [[HomeObject]] internal property references sayHiMixin, as shown in the picture above.

As super looks for parent methods in [[HomeObject]].[[Prototype]], that means it searches sayHiMixin.[[Prototype]].

## EventMixin

Now let’s make a mixin for real life.
An important feature of many browser objects (for instance) is that they can generate events. Events are a great way to “broadcast information” to anyone who wants it. So let’s make a mixin that allows us to easily add event-related functions to any class/object.

* The mixin will provide a method .trigger(name, [...data]) to “generate an event” when something important happens to it. The name argument is a name of the event, optionally followed by additional arguments with event data.
* Also the method .on(name, handler) that adds handler function as the listener to events with the given name. It will be called when an event with the given name triggers, and get the arguments from the .trigger call.
* …And the method .off(name, handler) that removes the handler listener.

After adding the mixin, an object user will be able to generate an event "login" when the visitor logs in. And another object, say, calendar may want to listen for such events to load the calendar for the logged-in person.

Or, a menu can generate the event "select" when a menu item is selected, and other objects may assign handlers to react on that event. And so on.

Here’s the code:
let eventMixin = {
  /**
   * Subscribe to event, usage:
   *  menu.on('select', function(item) { ... }
  */
  on(eventName, handler) {
    if (!this._eventHandlers) this._eventHandlers = {};
    if (!this._eventHandlers[eventName]) {
      this._eventHandlers[eventName] = [];
    }
    this._eventHandlers[eventName].push(handler);
  },

  /**
   * Cancel the subscription, usage:
   *  menu.off('select', handler)
   */
  off(eventName, handler) {
    let handlers = this._eventHandlers?.[eventName];
    if (!handlers) return;
    for (let i = 0; i < handlers.length; i++) {
      if (handlers[i] === handler) {
        handlers.splice(i--, 1);
      }
    }
  },

  /**
   * Generate an event with the given name and data
   *  this.trigger('select', data1, data2);
   */
  trigger(eventName, ...args) {
    if (!this._eventHandlers?.[eventName]) {
      return; // no handlers for that event name
    }

    // call the handlers
    this._eventHandlers[eventName].forEach(handler => handler.apply(this, args));
  }
};

.on(eventName, handler) – assigns function handler to run when the event with that name occurs. Technically, there’s an _eventHandlers property that stores an array of handlers for each event name, and it just adds it to the list.
.off(eventName, handler) – removes the function from the handlers list.
.trigger(eventName, ...args) – generates the event: all handlers from _eventHandlers[eventName] are called, with a list of arguments ...args.

Usage:
// Make a class
class Menu {
  choose(value) {
    this.trigger("select", value);
  }
}
// Add the mixin with event-related methods
Object.assign(Menu.prototype, eventMixin);

let menu = new Menu();

// add a handler, to be called on selection:
menu.on("select", value => alert(`Value selected: ${value}`));

// triggers the event => the handler above runs and shows:
// Value selected: 123
menu.choose("123");

Now, if we’d like any code to react to a menu selection, we can listen for it with menu.on(...).

And eventMixin mixin makes it easy to add such behavior to as many classes as we’d like, without interfering with the inheritance chain.

### Summary
Mixin – is a generic object-oriented programming term: a class that contains methods for other classes.

Some other languages allow multiple inheritance. JavaScript does not support multiple inheritance, but mixins can be implemented by copying methods into prototype.

We can use mixins as a way to augment a class by adding multiple behaviors, like event-handling as we have seen above.

Mixins may become a point of conflict if they accidentally overwrite existing class methods. So generally one should think well about the naming methods of a mixin, to minimize the probability of that happening.