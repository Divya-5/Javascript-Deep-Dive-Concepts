# Prototypal inheritance

In programming, we often want to take something and extend it.

For instance, we have a user object with its properties and methods, and want to make admin and guest as slightly modified variants of it. We’d like to reuse what we have in user, not copy/reimplement its methods, just build a new object on top of it.

Prototypal inheritance is a language feature that helps in that.

## [[Prototype]]

In JavaScript, objects have a special hidden property [[Prototype]] (as named in the specification), that is either null or references another object. That object is called “a prototype”:

![user reference](images/object-prototype-empty.svg "reference")

When we read a property from object, and it’s missing, JavaScript automatically takes it from the prototype. In programming, this is called “prototypal inheritance”. And soon we’ll study many examples of such inheritance, as well as cooler language features built upon it.

The property [[Prototype]] is internal and hidden, but there are many ways to set it.

One of them is to use the special name **proto**, like this:

Eg:
`let animal = {`\
`eats: true`\
`};`\
`let rabbit = {`\
`jumps: true`\
`};`\

`rabbit.**proto** = animal; (\*)`\

// we can find both properties in rabbit now:
`alert( rabbit.eats ); // true (\*\*)`\
`alert( rabbit.jumps ); // true`\

Here the line (\*) sets animal to be the prototype of rabbit.
Then, when alert tries to read property rabbit.eats (\*\*), it’s not in rabbit, so JavaScript follows the [[Prototype]] reference and finds it in animal (look from the bottom up):

![user reference](images/proto-animal-rabbit.svg "reference")

Here we can say that "animal is the prototype of rabbit" or "rabbit prototypically inherits from animal".

So if animal has a lot of useful properties and methods, then they become automatically available in rabbit. Such properties are called “inherited”.

If we have a method in animal, it can be called on rabbit:

Eg:

`let animal = {`\
`eats: true,`\
`walk() {`\
`alert("Animal walk");`\
`}`\
`};`\

`let rabbit = {`\
`jumps: true,`\
`**proto**: animal`\
`};`\

// walk is taken from the prototype
`rabbit.walk(); // Animal walk`\

The method is automatically taken from the prototype, like this:

![user reference](images/proto-animal-rabbit-walk.svg "reference")

The prototype chain can be longer:

Eg:

`let animal = {`\
`eats: true,`\
`walk() {`\
`alert("Animal walk");`\
`}`\
`};`

`let rabbit = {`\
`jumps: true,`\
`**proto**: animal`\
`};`

`let longEar = {`\
`earLength: 10,`\
`**proto**: rabbit`\
`};`\

// walk is taken from the prototype chain
`longEar.walk(); // Animal walk`\
`alert(longEar.jumps); // true (from rabbit)`\

![user reference](images/proto-animal-rabbit-chain.svg "reference")

Now if we read something from longEar, and it’s missing, JavaScript will look for it in rabbit, and then in animal.

There are only two limitations:

1. The references can’t go in circles. JavaScript will throw an error if we try to assign **proto** in a circle.
2. The value of **proto** can be either an object or null. Other types are ignored.

Also it may be obvious, but still: there can be only one [[Prototype]]. An object may not inherit from two others.

### **proto** is a historical getter/setter for [[Prototype]]

It’s a common mistake of anovice developers not to know the difference between these two.

Please note that **proto** is not the same as the internal [[Prototype]] property. It’s a getter/setter for [[Prototype]].

The **proto** property is a bit outdated. It exists for historical reasons, modern JavaScript suggests that we should use Object.getPrototypeOf/Object.setPrototypeOf functions instead that get/set the prototype.

By the specification, **proto** must only be supported by browsers. In fact though, all environments including server-side support **proto**, so we’re quite safe using it.

As the **proto** notation is a bit more intuitively obvious, we use it in the examples.

## Writing doesn’t use prototype'

The prototype is only used for reading properties.

Write/delete operations work directly with the object.

In the example below, we assign its own walk method to rabbit:

Eg:

`let animal = {`\
`eats: true,`\
`walk() {`\
`/_ this method won't be used by rabbit _/`\
`}`\
`};`\

`let rabbit = {`\
`**proto**: animal`\
`};`\

`rabbit.walk = function() {`\
`alert("Rabbit! Bounce-bounce!");`\
`};`\

`rabbit.walk(); // Rabbit! Bounce-bounce!`\

From now on, rabbit.walk() call finds the method immediately in the object and executes it, without using the prototype:

![user reference](images/proto-animal-rabbit-walk-2.svg"reference")

Accessor properties are an exception, as assignment is handled by a setter function. So writing to such a property is actually the same as calling a function.

For that reason admin.fullName works correctly in the code below:
Eg:

`let user = {`\
`name: "John",`\
`surname: "Smith",`\

`set fullName(value) {`\
`[this.name, this.surname] = value.split(" ");`\
`},`\

`get fullName() {`\
`return {`${this.name} ${this.surname}`};`\
`}`\
`};`\

`let admin = {`\
`**proto**: user,`\
`isAdmin: true`\
`};`\

`alert(admin.fullName); // John Smith (\*)`\

// setter triggers!
`admin.fullName = "Alice Cooper"; // (\*\*)`\

`alert(admin.fullName); // Alice Cooper, state of admin modified`\
`alert(user.fullName); // John Smith, state of user protected`\
Here in the line (\*) the property admin.fullName has a getter in the prototype user, so i

Here in the line (\*) the property admin.fullName has a getter in the prototype user, so it is called. And in the line (\*\*) the property has a setter in the prototype, so it is called.

## The value of “this”

An interesting question may arise in the example above: what’s the value of this inside set fullName(value)? Where are the properties this.name and this.surname written: into user or admin?

The answer is simple: this is not affected by prototypes at all.

No matter where the method is found: in an object or its prototype. In a method call, this is always the object before the dot.

So, the setter call admin.fullName= uses admin as this, not user.

That is actually a super-important thing, because we may have a big object with many methods, and have objects that inherit from it. And when the inheriting objects run the inherited methods, they will modify only their own states, not the state of the big object.
For instance, here animal represents a “method storage”, and rabbit makes use of it.

The call rabbit.sleep() sets this.isSleeping on the rabbit object:

Eg:

// animal has methods
`let animal = {`\
`walk() {`\
`if (!this.isSleeping) {`\
`alert("I walk");`\
`}`\
`},`\
`sleep() {`\
`this.isSleeping = true;`\
`}`\
`};`\

`let rabbit = {`\
`name: "White Rabbit",`\
`**proto**: animal`\
`};`\

// modifies rabbit.isSleeping
`rabbit.sleep();`\

`alert(rabbit.isSleeping); // true`\
`alert(animal.isSleeping); // undefined (no such property in the prototype)`\

![user reference](images/proto-animal-rabbit-walk-3.svg"reference")

If we had other objects, like bird, snake, etc., inheriting from animal, they would also gain access to methods of animal. But this in each method call would be the corresponding object, evaluated at the call-time (before dot), not animal. So when we write data into this, it is stored into these objects.

As a result, methods are shared, but the object state is not.

## for…in loop
The for..in loop iterates over inherited properties too.

Eg:

`let animal = {`\
`  eats: true`\
`};`\

`let rabbit = {`\
`  jumps: true,`\
`  __proto__: animal`\
`};`\

// Object.keys only returns own keys
`alert(Object.keys(rabbit)); // jumps`\

// for..in loops over both own and inherited keys
`for(let prop in rabbit) alert(prop); // jumps, then eats`\

If that’s not what we want, and we’d like to exclude inherited properties, there’s a built-in method obj.hasOwnProperty(key): it returns true if obj has its own (not inherited) property named key.

So we can filter out inherited properties (or do something else with them):

Eg:

`let animal = {`\
  `eats: true`\
`};`\

`let rabbit = {`\
`  jumps: true,`\
  `__proto__: animal`\
`};`\

`for(let prop in rabbit) {`\
`  let isOwn = rabbit.hasOwnProperty(prop);`\

`  if (isOwn) {`\
 `   alert("Our: ${prop}"); // Our: jumps`\
`  } else {`\
`    alert("Inherited: ${prop}"); // Inherited: eats`\
`  }`\
`}`\

Here we have the following inheritance chain: rabbit inherits from animal, that inherits from Object.prototype (because animal is a literal object {...}, so it’s by default), and then null above it:


![user reference](images/rabbit-animal-object.svg"reference")

Note, there’s one funny thing. Where is the method rabbit.hasOwnProperty coming from? We did not define it. Looking at the chain we can see that the method is provided by Object.prototype.hasOwnProperty. In other words, it’s inherited.

…But why does hasOwnProperty not appear in the for..in loop like eats and jumps do, if for..in lists inherited properties?

The answer is simple: it’s not enumerable. Just like all other properties of Object.prototype, it has enumerable:false flag. And for..in only lists enumerable properties. That’s why it and the rest of the Object.prototype properties are not listed.

## Summary
In JavaScript, all objects have a hidden [[Prototype]] property that’s either another object or null.

We can use obj.__proto__ to access it (a historical getter/setter, there are other ways, to be covered soon).
The object referenced by [[Prototype]] is called a “prototype”.

If we want to read a property of obj or call a method, and it doesn’t exist, then JavaScript tries to find it in the prototype.
Write/delete operations act directly on the object, they don’t use the prototype (assuming it’s a data property, not a setter).
f we call obj.method(), and the method is taken from the prototype, this still references obj. So methods always work with the current object even if they are inherited.
The for..in loop iterates over both its own and its inherited properties. All other key/value-getting methods only operate on the object itself.

1) Working with prototype

Here’s the code that creates a pair of objects, then modifies them.

Which values are shown in the process?

let animal = {
  jumps: null
};
let rabbit = {
  __proto__: animal,
  jumps: true
};

alert( rabbit.jumps ); // ? (1) true

delete rabbit.jumps;

alert( rabbit.jumps ); // ? (2) null 

delete animal.jumps;

alert( rabbit.jumps ); // ? (3) undefined

2) Searching algorithm

The task has two parts.

Given the following objects:

let head = {
  glasses: 1
};

let table = {
  pen: 3,
  __proto__:head
  };

let bed = {
  sheet: 1,
  pillow: 2,
    __proto__:table
};

let pockets = {
  money: 2000
  __proto__:
    __proto__:bed
};

alert( pockets.pen ); // 3
alert( bed.glasses ); // 1
alert( table.money ); // undefined

In modern engines, performance-wise, there’s no difference whether we take a property from an object or its prototype. They remember where the property was found and reuse it in the next request.
For instance, for pockets.glasses they remember where they found glasses (in head), and next time will search right there. They are also smart enough to update internal caches if something changes, so that optimization is safe.


3) Where does it write?
We have rabbit inheriting from animal.
If we call rabbit.eat(), which object receives the full property: animal or rabbit?

The answer: rabbit.

That’s because this is an object before the dot, so rabbit.eat() modifies rabbit.

Property lookup and execution are two different things.

The method rabbit.eat is first found in the prototype, then executed with this=rabbit.

4) Why are both hamsters full?
We have two hamsters: speedy and lazy inheriting from the general hamster object.

When we feed one of them, the other one is also full. Why? How can we fix it?

let hamster = {
  stomach: [],

  eat(food) {
    this.stomach.push(food);
  }
};

let speedy = {
  __proto__: hamster
};

let lazy = {
  __proto__: hamster
};

// This one found the food
speedy.eat("apple");
alert( speedy.stomach ); // apple

// This one also has it, why? fix please.
alert( lazy.stomach ); // apple


Let’s look carefully at what’s going on in the call speedy.eat("apple").

The method speedy.eat is found in the prototype (=hamster), then executed with this=speedy (the object before the dot).

Then this.stomach.push() needs to find stomach property and call push on it. It looks for stomach in this (=speedy), but nothing found.

Then it follows the prototype chain and finds stomach in hamster.

Then it calls push on it, adding the food into the stomach of the prototype.

So all hamsters share a single stomach!

Both for lazy.stomach.push(...) and speedy.stomach.push(), the property stomach is found in the prototype (as it’s not in the object itself), then the new data is pushed into it.

Please note that such thing doesn’t happen in case of a simple assignment this.stomach=:

let hamster = {
  stomach: [],

  eat(food) {
    // assign to this.stomach instead of this.stomach.push
    this.stomach = [food];
  }
};

let speedy = {
   __proto__: hamster
};

let lazy = {
  __proto__: hamster
};

// Speedy one found the food
speedy.eat("apple");
alert( speedy.stomach ); // apple

// Lazy one's stomach is empty
alert( lazy.stomach ); // <nothing>


Now all works fine, because this.stomach= does not perform a lookup of stomach. The value is written directly into this object.

Also we can totally avoid the problem by making sure that each hamster has their own stomach:

let hamster = {
  stomach: [],

  eat(food) {
    this.stomach.push(food);
  }
};

let speedy = {
  __proto__: hamster,
  stomach: []
};

let lazy = {
  __proto__: hamster,
  stomach: []
};

// Speedy one found the food
speedy.eat("apple");
alert( speedy.stomach ); // apple

// Lazy one's stomach is empty
alert( lazy.stomach ); // <nothing>

As a common solution, all properties that describe the state of a particular object, like stomach above, should be written into that object. That prevents such problems.


# F.prototype

Remember, new objects can be created with a constructor function, like new F().

If F.prototype is an object, then the new operator uses it to set [[Prototype]] for the new object.


Please note that F.prototype here means a regular property named "prototype" on F. It sounds something similar to the term “prototype”, but here we really mean a regular property with this name.

Eg:

`let animal = {`
`  eats: true`
`};`

`function Rabbit(name) {`
`  this.name = name;`
`}`

`Rabbit.prototype = animal;`

`let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal`

`alert( rabbit.eats ); // true`

Setting Rabbit.prototype = animal literally states the following: "When a new Rabbit is created, assign its [[Prototype]] to animal".


That’s the resulting picture:

![user reference](images/proto-constructor-animal-rabbit.svg "reference")

On the picture, "prototype" is a horizontal arrow, meaning a regular property, and [[Prototype]] is vertical, meaning the inheritance of rabbit from animal.

I F.prototype only used at new F time
F.prototype property is only used when new F is called, it assigns [[Prototype]] of the new object.

If, after the creation, F.prototype property changes (F.prototype = <another object>), then new objects created by new F will have another object as [[Prototype]], but already existing objects keep the old one.


## Default F.prototype, constructor property

Every function has the "prototype" property even if we don’t supply it.

The default "prototype" is an object with the only property constructor that points back to the function itself.

function Rabbit() {}

/* default prototype
Rabbit.prototype = { constructor: Rabbit };
*/
![user reference](images/function-prototype-constructor.svg "reference")

We can check it:


function Rabbit() {}
// by default:
// Rabbit.prototype = { constructor: Rabbit }

alert( Rabbit.prototype.constructor == Rabbit ); // true

Naturally, if we do nothing, the constructor property is available to all rabbits through [[Prototype]]:

function Rabbit() {}
// by default:
// Rabbit.prototype = { constructor: Rabbit }

let rabbit = new Rabbit(); // inherits from {constructor: Rabbit}

alert(rabbit.constructor == Rabbit); // true (from prototype)

![user reference](images/rabbit-prototype-constructor.svg "reference")

We can use constructor property to create a new object using the same constructor as the existing one.

function Rabbit(name) {
  this.name = name;
  alert(name);
}

let rabbit = new Rabbit("White Rabbit");

let rabbit2 = new rabbit.constructor("Black Rabbit");

That’s handy when we have an object, don’t know which constructor was used for it (e.g. it comes from a 3rd party library), and we need to create another one of the same kind.

But probably the most important thing about "constructor" is that…

…JavaScript itself does not ensure the right "constructor" value.

Yes, it exists in the default "prototype" for functions, but that’s all. What happens with it later – is totally on us.
In particular, if we replace the default prototype as a whole, then there will be no "constructor" in it.

function Rabbit() {}
Rabbit.prototype = {
  jumps: true
};

let rabbit = new Rabbit();
alert(rabbit.constructor === Rabbit); // false

So, to keep the right "constructor" we can choose to add/remove properties to the default "prototype" instead of overwriting it as a whole:

function Rabbit() {}

// Not overwrite Rabbit.prototype totally
// just add to it
Rabbit.prototype.jumps = true
// the default Rabbit.prototype.constructor is preserved

Or, alternatively, recreate the constructor property manually:

Rabbit.prototype = {
  jumps: true,
  constructor: Rabbit
};

// now constructor is also correct, because we added it


Summary

In this chapter we briefly described the way of setting a [[Prototype]] for objects created via a constructor function.
* The F.prototype property (don’t mistake it for [[Prototype]]) sets [[Prototype]] of new objects when new F() is called.
* The value of F.prototype should be either an object or null: other values won’t work.
* The "prototype" property only has such a special effect when set on a constructor function, and invoked with new.

On regular objects the prototype is nothing special:
let user = {
  name: "John",
  prototype: "Bla-bla" // no magic at all
};

By default all functions have F.prototype = { constructor: F }, so we can get the constructor of an object by accessing its "constructor" property.

### Changing "prototype"
In the code below we create new Rabbit, and then try to modify its prototype.
In the start, we have this code:

function Rabbit() {}
Rabbit.prototype = {
  eats: true
};

let rabbit = new Rabbit();

alert( rabbit.eats ); // true

1)

We added one more string (emphasized). What will alert show now?

function Rabbit() {}
Rabbit.prototype = {
  eats: true
};

let rabbit = new Rabbit();

Rabbit.prototype = {};

alert( rabbit.eats ); // true
The assignment to Rabbit.prototype sets up [[Prototype]] for new objects, but it does not affect the existing ones.

2)
…And if the code is like this (replaced one line)?

function Rabbit() {}
Rabbit.prototype = {
  eats: true
};

let rabbit = new Rabbit();

Rabbit.prototype.eats = false;

alert( rabbit.eats ); // false

Objects are assigned by reference. The object from Rabbit.prototype is not duplicated, it’s still a single object referenced both by Rabbit.prototype and by the [[Prototype]] of rabbit.

So when we change its content through one reference, it is visible through the other one.

3) 
And like this (replaced one line)?
function Rabbit() {}
Rabbit.prototype = {
  eats: true
};

let rabbit = new Rabbit();

delete rabbit.eats;

alert( rabbit.eats ); // true
All delete operations are applied directly to the object. Here delete rabbit.eats tries to remove eats property from rabbit, but it doesn’t have it. So the operation won’t have any effect.

4)
The last variant:

function Rabbit() {}
Rabbit.prototype = {
  eats: true
};

let rabbit = new Rabbit();

delete Rabbit.prototype.eats;

alert( rabbit.eats ); // undefined

The property eats is deleted from the prototype, it doesn’t exist any more.

### Create an object with the same constructor

Imagine, we have an arbitrary object obj, created by a constructor function – we don’t know which one, but we’d like to create a new object using it.
Can we do it like that?
let obj2 = new obj.constructor();

Give an example of a constructor function for obj which lets such code work right. And an example that makes it work wrong.

We can use such approach if we are sure that "constructor" property has the correct value.
For instance, if we don’t touch the default "prototype", then this code works for sure:

function User(name) {
  this.name = name;
}

let user = new User('John');
let user2 = new user.constructor('Pete');

alert( user2.name ); // Pete (worked!)

It worked, because User.prototype.constructor == User.

…But if someone, so to speak, overwrites User.prototype and forgets to recreate constructor to reference User, then it would fail.

function User(name) {
  this.name = name;
}
User.prototype = {}; // (*)

let user = new User('John');
let user2 = new user.constructor('Pete');

alert( user2.name ); // undefined
Why user2.name is undefined?

Here’s how new user.constructor('Pete') works:

First, it looks for constructor in user. Nothing.
Then it follows the prototype chain. The prototype of user is User.prototype, and it also has no constructor (because we “forgot” to set it right!).
Going further up the chain, User.prototype is a plain object, its prototype is the built-in Object.prototype.
Finally, for the built-in Object.prototype, there’s a built-in Object.prototype.constructor == Object. So it is used.
Finally, at the end, we have let user2 = new Object('Pete').

Probably, that’s not what we want. We’d like to create new User, not new Object. That’s the outcome of the missing constructor.

(Just in case you’re curious, the new Object(...) call converts its argument to an object. That’s a theoretical thing, in practice no one calls new Object with a value, and generally we don’t use new Object to make objects at all).