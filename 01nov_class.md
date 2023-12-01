# Class basic syntax

In object-oriented programming, a class is an extensible program-code-template for creating objects, providing initial values for state (member variables) and implementations of behavior (member functions or methods).

## The “class” syntax

The basic syntax is:

`class MyClass {`\
`// class methods`\
`constructor() { ... }`\
`method1() { ... }`\
`method2() { ... }`\
`method3() { ... }`\
`...`\
`}`\

Then use new MyClass() to create a new object with all the listed methods.
The constructor() method is called automatically by new, so we can initialize the object there.

Eg:

`class User {`\

`constructor(name) {`\
`this.name = name;`\
`}`\

`sayHi() {`\
`alert(this.name);`\
`}`\

`}`\

// Usage:
`let user = new User("John");`\
`user.sayHi();`\

When new User("John") is called:

1. A new object is created.
2. The constructor runs with the given argument and assigns it to this.name.

…Then we can call object methods, such as user.sayHi().

### Important
`No comma between class methods
A common pitfall for novice developers is to put a comma between class methods, which would result in a syntax error.
The notation here is not to be confused with object literals. Within the class, no commas are required.`

## What is a class?

In JavaScript, a class is a kind of function.

Eg:

`class User {`\
`constructor(name) { this.name = name; }`\
`sayHi() { alert(this.name); }`\
`}`

// proof: User is a function
`alert(typeof User); // function`\

What class User {...} construct really does is:
1. Creates a function named User, that becomes the result of the class declaration. The function code is taken from the constructor method (assumed empty if we don’t write such method).
2. Stores class methods, such as sayHi, in User.prototype.

After new User object is created, when we call its method, it’s taken from the prototype. So the object has access to class methods.
We can illustrate the result of class User declaration as:
![user reference](images/class-user.svg "reference")

Eg:

`class User {`\
`constructor(name) { this.name = name; }`\
`sayHi() { alert(this.name); }`\
`}`\

`// class is a function`\
`alert(typeof User); // function`\

`// ...or, more precisely, the constructor method`\
`alert(User === User.prototype.constructor); // true`\

`// The methods are in User.prototype, e.g:`\
`alert(User.prototype.sayHi); // the code of the sayHi method`\

`// there are exactly two methods in the prototype`\
`alert(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi`\

## Not just a syntactic sugar

Sometimes people say that class is a “syntactic sugar” (syntax that is designed to make things easier to read, but doesn’t introduce anything new), because we could actually declare the same thing without using the class keyword at all:

Eg:

`// rewriting class User in pure functions`\
`// 1. Create constructor function`\
`function User(name) {`\
`this.name = name;`\
`}`\
`// a function prototype has "constructor" property by default,`\
`// so we don't need to create it`\

`// 2. Add the method to prototype`\
`User.prototype.sayHi = function() {`\
`alert(this.name);`\
`};`\

`// Usage:`\
`let user = new User("John");`\
`user.sayHi();`\

The result of this definition is about the same. So, there are indeed reasons why class can be considered a syntactic sugar to define a constructor together with its prototype methods.
Still, there are important differences.

1. First, a function created by class is labelled by a special internal property [[IsClassConstructor]]: true. So it’s not entirely the same as creating it manually.

The language checks for that property in a variety of places. For example, unlike a regular function, it must be called with new:

Eg:

`class User {`\
`constructor() {}`\
`}`\

`alert(typeof User); // function`\
`User(); // Error: Class constructor User cannot be invoked without 'new'`\

Also, a string representation of a class constructor in most JavaScript engines starts with the “class…”

Eg:

`class User {`\
`constructor() {}`\
`}`\

`alert(User); // class User { ... }`\

2. Class methods are non-enumerable. A class definition sets enumerable flag to false for all methods in the "prototype".
   That’s good, because if we for..in over an object, we usually don’t want its class methods.
### An enumerable property in JavaScript means that a property can be viewed if it is iterated using the for…in loop or Object. keys() method. All the properties which are created by simple assignment or property initializer are enumerable by default.

3. Classes always use strict. All code inside the class construct is automatically in strict mode.

## Class Expression

Just like functions, classes can be defined inside another expression, passed around, returned, assigned, etc.

Eg:

`let User = class {`\
`sayHi() {`\
`alert("Hello");`\
`}`\
`};`\

Similar to Named Function Expressions, class expressions may have a name.

If a class expression has a name, it’s visible inside the class only:

// "Named Class Expression"
// (no such term in the spec, but that's similar to Named Function Expression)
`let User = class MyClass {`\
`sayHi() {`\
`alert(MyClass); // MyClass name is visible only inside the class`\
`}`\
`};`\

`new User().sayHi(); // works, shows MyClass definition`\

`alert(MyClass); // error, MyClass name isn't visible outside of the class`\

We can even make classes dynamically “on-demand”, like this:

Eg:

`function makeClass(phrase) {`\
`// declare a class and return it`\
`return class {`\
`sayHi() {`\
`alert(phrase);`\
`}`\
`};`\
`}`\

`// Create a new class`\
`let User = makeClass("Hello");`\
`new User().sayHi(); // Hello`\

## Getters/setters

Just like literal objects, classes may include getters/setters, computed properties etc.

Eg:

`class User {`\
`constructor(name) {`\
`// invokes the setter`\
`this.name = name;`\
`}`\

`get name() {`\
`return this.\_name;`\
`}`\

`set name(value) {`\
`if (value.length < 4) {`\
`alert("Name is too short.");`\
`return;`\
`}`\
`this.\_name = value;`\
`}`\

`}`\

`let user = new User("John");`\
`alert(user.name); // John`\

`user = new User(""); // Name is too short.`\

## Computed names […]

Eg:

`class User {`\

`['say' + 'Hi']() {`\
`alert("Hello");`\
`}`\

`}`\

`new User().sayHi();`\

Such features are easy to remember, as they resemble that of literal objects.

## Class fields

Old browsers may need a polyfill
Class fields are a recent addition to the language.

our classes only had methods.
“Class fields” is a syntax that allows to add any properties.
For instance, let’s add name property to class User:

Eg:

`class User {`\
`name = "John";`\

`sayHi() {`\
`alert(`Hello, ${this.name}!`);`\
`}`\
`}`\

`new User().sayHi(); // Hello, John!`\

So, we just write " = " in the declaration, and that’s it.

The important difference of class fields is that they are set on individual objects, not User.prototype:

`class User {`\
`name = "John";`\
`}`\

`let user = new User();`\
`alert(user.name); // John`\
`alert(User.prototype.name); // undefined`\

We can also assign values using more complex expressions and function calls:

`class User {`\
`name = prompt("Name, please?", "John");`\
`}`\

`let user = new User();`\
`alert(user.name); // John`\

## Making bound methods with class fields

So if an object method is passed around and called in another context, this won’t be a reference to its object any more.

For instance, this code will show undefined:

`class Button {`\
`constructor(value) {`\
`this.value = value;`\
`}`\

`click() {`\
`alert(this.value);`\
`}`\
`}`\

`let button = new Button("hello");`\

`setTimeout(button.click, 1000); // undefined`\

The problem is called "losing this".

There are two approaches to fixing it,

`class Button {`\
`constructor(value) {`\
`this.value = value;\`
`}`\
`click = () => {`\
`alert(this.value);`\
`}`\
`}`\

`let button = new Button("hello");`\

`setTimeout(button.click, 1000); // hello`\

The class field click = () => {...} is created on a per-object basis, there’s a separate function for each Button object, with this inside it referencing that object. We can pass button.click around anywhere, and the value of this will always be correct.

## Summary

The basic class syntax looks like this:

class MyClass {
prop = value; // property

constructor(...) { // constructor
// ...
}

method(...) {} // method

get something(...) {} // getter method
set something(...) {} // setter method

[Symbol.iterator]() {} // method with computed name (symbol here)
// ...
}

MyClass is technically a function (the one that we provide as constructor), while methods, getters and setters are written to MyClass.prototype.



