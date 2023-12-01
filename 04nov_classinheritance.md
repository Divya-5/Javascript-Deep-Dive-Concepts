# Class inheritance

Class inheritance is a way for one class to extend another class.
So we can create new functionality on top of the existing.

## The “extends” keyword

Eg:

`class Animal {`\
`constructor(name) {`\
`this.speed = 0;`\
`this.name = name;`\
`}`\
`run(speed) {`\
`this.speed = speed;`\
`alert(`${this.name} runs with speed ${this.speed}.`);`\
`}`\
`stop() {`\
`this.speed = 0;`\
`alert(`${this.name} stands still.`);`\
`}`\
`}`\

`let animal = new Animal("My animal");`\

Here’s how we can represent animal object and Animal class graphically:
![user reference](images/rabbit-animal-independent-animal.svg "reference")

…And we would like to create another class Rabbit.

As rabbits are animals, Rabbit class should be based on Animal, have access to animal methods, so that rabbits can do what “generic” animals can do.

The syntax to extend another class is: class Child extends Parent.

Let’s create class Rabbit that inherits from Animal:

Eg:

`class Rabbit extends Animal {`\
`hide() {`\
`alert(`${this.name} hides!`);`\
`}`\
`}`\

`let rabbit = new Rabbit("White Rabbit");`\

`rabbit.run(5); // White Rabbit runs with speed 5.`\
`rabbit.hide(); // White Rabbit hides!`\

Object of Rabbit class have access both to Rabbit methods, such as rabbit.hide(), and also to Animal methods, such as rabbit.run().

Internally, extends keyword works using the good old prototype mechanics. It sets Rabbit.prototype.[[Prototype]] to Animal.prototype. So, if a method is not found in Rabbit.prototype, JavaScript takes it from Animal.prototype.
![user reference](images/animal-rabbit-extends.svg "reference")

For instance, to find rabbit.run method, the engine checks (bottom-up on the picture):

1. The rabbit object (has no run).
2. Its prototype, that is Rabbit.prototype (has hide, but not run).
3. Its prototype, that is (due to extends) Animal.prototype, that finally has the run method.

JavaScript itself uses prototypal inheritance for built-in objects. E.g. Date.prototype.[[Prototype]] is Object.prototype. That’s why dates have access to generic object methods.

### Important

Any expression is allowed after extends

Class syntax allows to specify not just a class, but any expression after extends.

For instance, a function call that generates the parent class:

Eg:

`function f(phrase) {`\
`return class {`\
`sayHi() { alert(phrase); }`\
`};`\
`}`\

`class User extends f("Hello") {}`\

`new User().sayHi(); // Hello`\

Here class User inherits from the result of f("Hello").

That may be useful for advanced programming patterns when we use functions to generate classes depending on many conditions and can inherit from them.

## Overriding a method

Now let’s move forward and override a method. By default, all methods that are not specified in class Rabbit are taken directly “as is” from class Animal.

But if we specify our own method in Rabbit, such as stop() then it will be used instead:

Eg:

`class Rabbit extends Animal {`\
`stop() {`\
`// ...now this will be used for rabbit.stop()`\
`// instead of stop() from class Animal`\
`}`\
`}`\

Usually, however, we don’t want to totally replace a parent method, but rather to build on top of it to tweak or extend its functionality. We do something in our method, but call the parent method before/after it or in the process.

Classes provide "super" keyword for that.

1. super.method(...) to call a parent method.
2. super(...) to call a parent constructor (inside our constructor only).

For instance, let our rabbit autohide when stopped:

Eg:

`class Animal {`\

`constructor(name) {`\
`this.speed = 0;`\
`this.name = name;`\
`}`\

`run(speed) {`\
`this.speed = speed;`\
`alert(`${this.name} runs with speed ${this.speed}.`);`\
`}`\

`stop() {`\
`this.speed = 0;`\
`alert(`${this.name} stands still.`);`\
`}`\

`}`\

`class Rabbit extends Animal {`\
`hide() {`\
`alert(`${this.name} hides!`);`\
`}`\

`stop() {`\
`super.stop(); // call parent stop`\
`this.hide(); // and then hide`\
`}`\
`}`\

`let rabbit = new Rabbit("White Rabbit");`\

`rabbit.run(5); // White Rabbit runs with speed 5.`\
`rabbit.stop(); // White Rabbit stands still. White Rabbit hides!`\

Now Rabbit has the stop method that calls the parent super.stop() in the process.

### Important

Arrow functions have no super
arrow functions do not have super.

If accessed, it’s taken from the outer function.

Eg:

`class Rabbit extends Animal {`\
`stop() {`\
`setTimeout(() => super.stop(), 1000); // call parent stop after 1sec`\
`}`\
`}`\

The super in the arrow function is the same as in stop(), so it works as intended. If we specified a “regular” function here, there would be an error:

// Unexpected super
`setTimeout(function() { super.stop() }, 1000); //Uncaught SyntaxError: 'super' keyword unexpected here`\

## Overriding constructor

With constructors it gets a little bit tricky. Until now, Rabbit did not have its own constructor.
if a class extends another class and has no constructor, then the following “empty” constructor is generated:

Eg:

`class Rabbit extends Animal {`\
`// generated for extending classes without own constructors`\
`constructor(...args) {`\
`super(...args);`\
`}`\
`}`\

As we can see, it basically calls the parent constructor passing it all the arguments. That happens if we don’t write a constructor of our own.
Now let’s add a custom constructor to Rabbit. It will specify the earLength in addition to name:

Eg:

`class Animal {`\
`constructor(name) {`\
`this.speed = 0;`\
`this.name = name;`\
`}`\
`// ...`\
`}`\

`class Rabbit extends Animal {`\
`constructor(name, earLength) {`\
`this.speed = 0;`\
`this.name = name;`\
`this.earLength = earLength;`\
`}`\

`// ...`\
`}`\

// Doesn't work!
`let rabbit = new Rabbit("White Rabbit", 10); // Error: this is not defined.`\

Whoops! We’ve got an error. Now we can’t create rabbits. What went wrong?

The short answer is:

- Constructors in inheriting classes must call super(...), and (!) do it before using this.

In JavaScript, there’s a distinction between a constructor function of an inheriting class (so-called “derived constructor”) and other functions. A derived constructor has a special internal property [[ConstructorKind]]:"derived". That’s a special internal label.

That label affects its behavior with new.

When a regular function is executed with new, it creates an empty object and assigns it to this.
But when a derived constructor runs, it doesn’t do this. It expects the parent constructor to do this job.
So a derived constructor must call super in order to execute its parent (base) constructor, otherwise the object for this won’t be created. And we’ll get an error.
For the Rabbit constructor to work, it needs to call super() before using this, like here:

Eg:

`class Animal {`\

`constructor(name) {`\
`this.speed = 0;`\
`this.name = name;`\
`}`\

`// ...`\
`}`\

`class Rabbit extends Animal {`\

`constructor(name, earLength) {`\
`super(name);`\
`this.earLength = earLength;`\
`}`\

`// ...`\
`}`\

// now fine
`let rabbit = new Rabbit("White Rabbit", 10);`\
`alert(rabbit.name); // White Rabbit`\
`alert(rabbit.earLength); // 10`\

## Overriding class fields: a tricky note

We can override not only methods, but also class fields.
Although, there’s a tricky behavior when we access an overridden field in parent constructor, quite different from most other programming languages.
Consider this example:

`class Animal {`\
`name = 'animal';`\

`constructor() {\
`alert(this.name); // (\*)` \
``} `
`}`

`class Rabbit extends Animal {`\
`name = 'rabbit';`\
`}`\

`new Animal(); // animal`\
`new Rabbit(); // animal`\

Here, class Rabbit extends Animal and overrides the name field with its own value.

There’s no own constructor in Rabbit, so Animal constructor is called.

What’s interesting is that in both cases: new Animal() and new Rabbit(), the alert in the line (\*) shows animal.

In other words, the parent constructor always uses its own field value, not the overridden one.

What’s odd about it?

If it’s not clear yet, please compare with methods.

Here’s the same code, but instead of this.name field we call this.showName() method:

Eg:

`class Animal {`\
`showName() { // instead of this.name = 'animal'`\
`alert('animal');`\
`}`\

`constructor() {`\
`this.showName(); // instead of alert(this.name);`\
`}`\
`}`\

`class Rabbit extends Animal {`\
`showName() {`\
`alert('rabbit');`\
`}`\
`}`\

`new Animal(); // animal`\
`new Rabbit(); // rabbit`\

Please note: now the output is different.

And that’s what we naturally expect. When the parent constructor is called in the derived class, it uses the overridden method.

…But for class fields it’s not so. As said, the parent constructor always uses the parent field.

Why is there a difference?

Well, the reason is the field initialization order. The class field is initialized:

Before constructor for the base class (that doesn’t extend anything),
Immediately after super() for the derived class.
In our case, Rabbit is the derived class. There’s no constructor() in it. As said previously, that’s the same as if there was an empty constructor with only super(...args).

So, new Rabbit() calls super(), thus executing the parent constructor, and (per the rule for derived classes) only after that its class fields are initialized. At the time of the parent constructor execution, there are no Rabbit class fields yet, that’s why Animal fields are used.

This subtle difference between fields and methods is specific to JavaScript.

Luckily, this behavior only reveals itself if an overridden field is used in the parent constructor. Then it may be difficult to understand what’s going on, so we’re explaining it here.

If it becomes a problem, one can fix it by using methods or getters/setters instead of fields.

## Summary

1. To extend a class: class Child extends Parent:

- That means Child.prototype.**proto** will be Parent.prototype, so methods are inherited.

2. When overriding a constructor:

- We must call parent constructor as super() in Child constructor before using this.

3. When overriding another method:

- We can use super.method() in a Child method to call Parent method.

Also:

- Arrow functions don’t have their own this or super, so they transparently fit into the surrounding context.

# Static properties and methods

We can also assign a method to the class as a whole. Such methods are called static.

In a class declaration, they are prepended by static keyword, like this:

class User {
static staticMethod() {
alert(this === User);
}
}

User.staticMethod(); // true

The value of this in User.staticMethod() call is the class constructor User itself (the “object before dot” rule).
Usually, static methods are used to implement functions that belong to the class as a whole, but not to any particular object of it.
For instance, we have Article objects and need a function to compare them.

A natural solution would be to add Article.compare static method:

Eg:

class Article {
constructor(title, date) {
this.title = title;
this.date = date;
}

static compare(articleA, articleB) {
return articleA.date - articleB.date;
}
}

// usage
let articles = [
new Article("HTML", new Date(2019, 1, 1)),
new Article("CSS", new Date(2019, 0, 1)),
new Article("JavaScript", new Date(2019, 11, 1))
];

articles.sort(Article.compare);

alert( articles[0].title ); // CSS
Here Article.compare method stands “above” articles, as a means to compare them. It’s not a method of an article, but rather of the whole class.

Another example would be a so-called “factory” method.

Let’s say, we need multiple ways to create an article:

1. Create by given parameters (title, date etc).
2. Create an empty article with today’s date.
   …or else somehow.

The first way can be implemented by the constructor. And for the second one we can make a static method of the class.
Such as Article.createTodays() here:

class Article {
constructor(title, date) {
this.title = title;
this.date = date;
}

static createTodays() {
// remember, this = Article
return new this("Today's digest", new Date());
}
}

let article = Article.createTodays();

alert( article.title ); // Today's digest

Now every time we need to create a today’s digest, we can call Article.createTodays(). Once again, that’s not a method of an article, but a method of the whole class.

Static methods are also used in database-related classes to search/save/remove entries from the database, like this:

// assuming Article is a special class for managing articles
// static method to remove the article by id:
Article.remove({id: 12345});

### Important

Static methods aren’t available for individual objects
Static methods are callable on classes, not on individual objects.

E.g. such code won’t work:
article.createTodays(); /// Error: article.createTodays is not a function

## Static properties

Static properties are also possible, they look like regular class properties, but prepended by static:

Eg:
class Article {
static publisher = "Ilya Kantor";
}

alert( Article.publisher ); // Ilya Kantor

That is the same as a direct assignment to Article:

Article.publisher = "Ilya Kantor";

## Inheritance of static properties and methods

Static properties and methods are inherited.
For instance, Animal.compare and Animal.planet in the code below are inherited and accessible as Rabbit.compare and Rabbit.planet:

class Animal {
static planet = "Earth";

constructor(name, speed) {
this.speed = speed;
this.name = name;
}

run(speed = 0) {
this.speed += speed;
alert(`${this.name} runs with speed ${this.speed}.`);
}

static compare(animalA, animalB) {
return animalA.speed - animalB.speed;
}

}

// Inherit from Animal
class Rabbit extends Animal {
hide() {
alert(`${this.name} hides!`);
}
}

let rabbits = [
new Rabbit("White Rabbit", 10),
new Rabbit("Black Rabbit", 5)
];

rabbits.sort(Rabbit.compare);

rabbits[0].run(); // Black Rabbit runs with speed 5.

alert(Rabbit.planet); // Earth

Now when we call Rabbit.compare, the inherited Animal.compare will be called.
How does it work? Again, using prototypes. As you might have already guessed, extends gives Rabbit the [[Prototype]] reference to Animal.
![user reference](images/animal-rabbit-static.svg "reference")

So, Rabbit extends Animal creates two [[Prototype]] references:

1. Rabbit function prototypally inherits from Animal function.
2. Rabbit.prototype prototypally inherits from Animal.prototype.

As a result, inheritance works both for regular and static methods.

Here, let’s check that by code:

class Animal {}
class Rabbit extends Animal {}

// for statics
alert(Rabbit.**proto** === Animal); // true

// for regular methods
alert(Rabbit.prototype.**proto** === Animal.prototype); // true

## Summary

Static methods are used for the functionality that belongs to the class “as a whole”. It doesn’t relate to a concrete class instance.

For example, a method for comparison Article.compare(article1, article2) or a factory method Article.createTodays().

They are labeled by the word static in class declaration.

Static properties are used when we’d like to store class-level data, also not bound to an instance.

The syntax is:

class MyClass {
static property = ...;

static method() {
...
}
}

Technically, static declaration is the same as assigning to the class itself:

MyClass.property = ...
MyClass.method = ...

For class B extends A the prototype of the class B itself points to A: B.[[Prototype]] = A. So if a field is not found in B, the search continues in A.

## Tasks

### Class extends Object?

As we know, all objects normally inherit from Object.prototype and get access to “generic” object methods like hasOwnProperty etc.

class Rabbit {
constructor(name) {
this.name = name;
}
}

let rabbit = new Rabbit("Rab");

// hasOwnProperty method is from Object.prototype
alert( rabbit.hasOwnProperty('name') ); // true

But if we spell it out explicitly like "class Rabbit extends Object", then the result would be different from a simple "class Rabbit"?

What’s the difference?

Here’s an example of such code (it doesn’t work – why? fix it?):

class Rabbit extends Object {
constructor(name) {
this.name = name;
}
}

let rabbit = new Rabbit("Rab");

alert( rabbit.hasOwnProperty('name') ); // Error

First, let’s see why the latter code doesn’t work.

The reason becomes obvious if we try to run it. An inheriting class constructor must call super(). Otherwise "this" won’t be “defined”.

So here’s the fix:

class Rabbit extends Object {
constructor(name) {
super(); // need to call the parent constructor when inheriting
this.name = name;
}
}

let rabbit = new Rabbit("Rab");

alert( rabbit.hasOwnProperty('name') ); // true

But that’s not all yet.

Even after the fix, there’s still an important difference between "class Rabbit extends Object" and class Rabbit

As we know, the “extends” syntax sets up two prototypes:

1. Between "prototype" of the constructor functions (for methods).
2. Between the constructor functions themselves (for static methods).

In the case of class Rabbit extends Object it means:

class Rabbit extends Object {}

alert( Rabbit.prototype.**proto** === Object.prototype ); // (1) true
alert( Rabbit.**proto** === Object ); // (2) true

So Rabbit now provides access to the static methods of Object via Rabbit, like this:

class Rabbit extends Object {}

// normally we call Object.getOwnPropertyNames
alert ( Rabbit.getOwnPropertyNames({a: 1, b: 2}));

But if we don’t have extends Object, then Rabbit.**proto** is not set to Object.

Here’s the demo:

class Rabbit {}

alert( Rabbit.prototype.**proto** === Object.prototype ); // (1) true
alert( Rabbit.**proto** === Object ); // (2) false (!)
alert( Rabbit.**proto** === Function.prototype ); // as any function by default

// error, no such function in Rabbit
alert ( Rabbit.getOwnPropertyNames({a: 1, b: 2})); // Error
So Rabbit doesn’t provide access to static methods of Object in that case.

By the way, Function.prototype also has “generic” function methods, like call, bind etc. They are ultimately available in both cases, because for the built-in Object constructor, Object.**proto** === Function.prototype.

Here’s the picture:

![user reference](images/rabbit-extends-object.svg "reference")

So, to put it short, there are two differences:


* class Rabbit	
Rabbit.__proto__ === Function.prototype	

* class Rabbit extends Object
needs to call super() in constructor
Rabbit.__proto__ === Object