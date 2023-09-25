# What is this?
In JavaScript, the this keyword refers to an object.
Which object depends on how this is being invoked (used or called).
The this keyword refers to different objects depending on how it is used:
* In an object method, this refers to the object.
* Alone, this refers to the global object.
* In a function, this refers to the global object.
* In a function, in strict mode, this is undefined.
* In an event, this refers to the element that received the event.
* Methods like call(), apply(), and bind() can refer this to any object.

## this is not a variable. It is a keyword. You cannot change the value of this.
The value of this depends on in which context it appears: function, class, or global.

# Function context
Inside a function, the value of this depends on how the function is called. 

Eg)

// An object can be passed as the first argument to 'call'
// or 'apply' and 'this' will be bound to it.
`const obj = { a: "Custom" };`\

// Variables declared with var become properties of 'globalThis'\
`var a = "Global";`

`function whatsThis() {`\
  `return this.a; // 'this' depends on how the function is called`\
`}`

`whatsThis(); // 'Global'; the 'this' parameter defaults to 'globalThis' in non–strict mode`\
`obj.whatsThis = whatsThis;`\
`obj.whatsThis(); // 'Custom'; the 'this' parameter is bound to obj`

Eg)

`function add(c, d) {`\
`  return this.a + this.b + c + d;`\
`}`

`const o = { a: 1, b: 3 };`\

// The first argument is bound to the implicit 'this' parameter; the remaining
// arguments are bound to the named parameters.
`add.call(o, 5, 7); // 16`

// The first argument is bound to the implicit 'this' parameter; the second
// argument is an array whose members are bound to the named parameters.
`add.apply(o, [10, 20]); // 34`


For a typical function, the value of this is the object that the function is accessed on. In other words, if the function call is in the form obj.f(), then this refers to obj. 

Eg)

`function getThis() {`\
`  return this;`\
`}`

`const obj1 = { name: "obj1" };`\
`const obj2 = { name: "obj2" };`

`obj1.getThis = getThis;`\
`obj2.getThis = getThis;`

`console.log(obj1.getThis()); // { name: 'obj1', getThis: [Function: getThis] }`\
`console.log(obj2.getThis()); // { name: 'obj2', getThis: [Function: getThis] }`

Note how the function is the same, but based on how it's invoked, the value of this is different. This is analogous to how function parameters work.
The value of this is not the object that has the function as an own property, but the object that is used to call the function. 
__proto__ is a keyword to point one object with another object. It is a pointer. Inheritance is happening through proto. This is known as prototype chaining.

Eg)

`const obj3 = {`\
`  __proto__: obj1,` \
`  name: "obj3",`\
`};`

`console.log(obj3.getThis()); // { name: 'obj3' }`

The value of this always changes based on how a function is called, even when the function was defined on an object at creation:

Eg)

`const obj4 = {`\
`  name: "obj4",`\
`  getThis() {`\
`    return this;`\
`  },`\
`};`

`const obj5 = { name: "obj5" };`

`obj5.getThis = obj4.getThis;`\
`console.log(obj5.getThis()); // { name: 'obj5', getThis: [Function: getThis] }`

If the value that the method is accessed on is a primitive, this will be a primitive value as well — but only if the function is in strict mode.

Eg)

`function getThisStrict() {`\
`  "use strict"; // Enter strict mode`\
`  return this;`\
`}`

`// Only for demonstration — you should not mutate built-in prototypes`\
`Number.prototype.getThisStrict = getThisStrict;`\
`console.log(typeof (1).getThisStrict()); // "number"`

If the function is called without being accessed on anything, this will be undefined — but only if the function is in strict mode.

Eg)

`function getThisStrict() {`\
`  "use strict"; // Enter strict mode`\
`  return this;`\
`}`\
`console.log(typeof getThisStrict()); // "undefined"`

In non-strict mode, a special process called this substitution ensures that the value of this is always an object. This means:
* If a function is called with this set to undefined or null, this gets substituted with globalThis.
* If the function is called with this set to a primitive value, this gets substituted with the primitive value's wrapper object.

Eg)

`  function getThis() {`\
`  return this;`\
`}`

`// Only for demonstration — you should not mutate built-in prototypes`\
`Number.prototype.getThis = getThis;`\
`console.log(typeof (1).getThis()); // "object"`\
`console.log(getThis() === globalThis); // true`

# Callbacks
When a function is passed as a callback, the value of this depends on how the callback is called.Callbacks are typically called with a this value of undefined (calling it directly without attaching it to any object), which means if the function is non–strict, the value of this is the global object (globalThis). This is the case for iterative array methods, the Promise() constructor, etc.
Eg)
`function logThis() {`\
`  "use strict";`\
`  console.log(this);`\
`}`

`[1, 2, 3].forEach(logThis); // undefined, undefined, undefined`
`[1, 2, 3].forEach(logThis, { name: "obj" }); // { name: 'obj' }, { name: 'obj' }, { name: 'obj' }`

# Arrow functions
Arrow functions are special: they don’t have their “own” this. If we reference this from such a function, it’s taken from the outer “normal” function.
For instance, here arrow() uses this from the outer user.sayHi() method:

Eg)

`let user = {`\
`  firstName: "Ilya",`\
`  sayHi() {`\
`    let arrow = () => alert(this.firstName);`\
 `   arrow();`\
`  }`\
`};`

`user.sayHi(); // Ilya`

That’s a special feature of arrow functions, it’s useful when we actually do not want to have a separate this, but rather to take it from the outer context.

In arrow functions, this retains the value of the enclosing lexical context's this. In other words, when evaluating an arrow function's body, the language does not create a new this binding.
For example, in global code, this is always globalThis regardless of strictness, because of the global context binding:

Eg)

`const globalObject = this;`\
`const foo = () => this;`\
`console.log(foo() === globalObject); // true`

Arrow functions create a closure over the this value of its surrounding scope, which means arrow functions behave as if they are "auto-bound" — no matter how it's invoked, this is bound to what it was when the function was created (in the example above, the global object). The same applies to arrow functions created inside other functions: their this remains that of the enclosing lexical context.

Eg)

`const obj = { name: "obj" };`\

`// Attempt to set this using call`\
`console.log(foo.call(obj) === globalObject); // true`

`// Attempt to set this using bind`\
`const boundFoo = foo.bind(obj);`\
`console.log(boundFoo() === globalObject); // true`

# Constructors
When a function is used as a constructor (with the new keyword), its this is bound to the new object being constructed, no matter which object the constructor function is accessed on. The value of this becomes the value of the new expression unless the constructor returns another non–primitive value.

Eg)

`function C() {`\
`  this.a = 37;`\
`}`

`let o = new C();`\
`console.log(o.a); // 37`

`function C2() {`\
`  this.a = 37;`\
`  return { a: 38 };`\
`}`

`o = new C2();`\
`console.log(o.a); // 38`

In the second example (C2), because an object was returned during construction, the new object that this was bound to gets discarded. (This essentially makes the statement this.a = 37; dead code. It's not exactly dead because it gets executed, but it can be eliminated with no outside effects.)

# this in a Method

Eg)

`let user = {`\
`  name: "John",`\
`  age: 30`\
`};`

`user.sayHi = function() {`\
`  alert("Hello!");`\
`};`

`user.sayHi(); // Hello!`

A function that is a property of an object is called its method.
we could use a pre-declared function as a method, like this:

Eg)

`let user = {`\
`  name: "John",`\
`  age: 30`\
`};`

// first, declare\
`function sayHi() {`\
`  alert("Hello!");`\
`}`

// then add as a method\
`user.sayHi = sayHi;`\
`user.sayHi(); // Hello!`

### Method shorthand

Eg)

`user = {`\
`  sayHi: function() {`\
`    alert("Hello");`\
`  }`\
`};`

// method shorthand looks better, right?\
`user = {`\
`  sayHi() { // same as "sayHi: function(){...}"`\
`    alert("Hello");`\
`  }`\
`};`\

As demonstrated, we can omit "function" and just write sayHi().

When used in an object method, this refers to the object.In the example, this refers to the person object.Because the fullName method is a method of the person object.

Eg) 

`const person = {` \
`firstName: "John",` \
`lastName : "Doe",` \
`id       : 5566,`  \
`fullName : function() {` \
` return this.firstName + " " + this.lastName; //John Doe`\
`}`\
`};`

Eg)

`const test = {`\
`  prop: 42,`\
`  func: function () {`\
  `  return this.prop;`\
`  },`\
`};`

`console.log(test.func());// Expected output: 42`

It’s common that an object method needs to access the information stored in the object to do its job.
To access the object, a method can use the this keyword.The value of this is the object “before dot”, the one used to call the method.

Eg)

`let user = {`\
`  name: "John",`\
`  age: 30,`

`  sayHi() {`\
`    // "this" is the "current object"`\
`    alert(this.name);`\
`  }`\
`};`

`user.sayHi(); // John`

Technically, it’s also possible to access the object without this, by referencing it via the outer variable:

Eg)

`let user = {`\
`  name: "John",`\
`  age: 30,`\

`  sayHi() {`\
 `   alert(user.name); // "user" instead of "this"`\
`  }`\
`};`

But such code is unreliable. If we decide to copy user to another variable, e.g. admin = user and overwrite user with something else, then it will access the wrong object.

That’s demonstrated below:

Eg)

`let user = {`\
`  name: "John",`\
`  age: 30,`

`  sayHi() {`\
`    alert( user.name ); // leads to an error`\
`  }`

`};`\
`let admin = user;`\
`user = null; // overwrite to make things obvious`

`admin.sayHi(); // TypeError: Cannot read property 'name' of null`\
If we used this.name instead of user.name inside the alert, then the code would work.

# this is not bound
In JavaScript, keyword this behaves unlike most other programming languages. It can be used in any function, even if it’s not a method of an object.
There’s no syntax error in the following example:
Eg)
`function sayHi() {`\
`  alert( this.name );`\
`}`

The value of this is evaluated during the run-time, depending on the context. For instance, here the same function is assigned to two different objects and has different “this” in the calls:

Eg)

`let user = { name: "John" };`\
`let admin = { name: "Admin" };`

`function sayHi() {`\
`  alert( this.name );`\
`}`

`// use the same function in two objects`\
`user.f = sayHi;`\
`admin.f = sayHi;`

`// these calls have different this`\
`// "this" inside the function is the object "before the dot"`\
`user.f(); // John  (this == user)`\
`admin.f(); // Admin  (this == admin)`

`admin['f'](); // Admin (dot or square brackets access the method – doesn't matter)`\

The rule is simple: if obj.f() is called, then this is obj during the call of f. So it’s either user or admin in the example above.
Calling without an object: this == undefined
We can even call the function without an object at all:

Eg)

`function sayHi() {`\
`  alert(this);`\
`}`

`sayHi(); // undefined`\
In this case this is undefined in strict mode. If we try to access this.name, there will be an error.

In non-strict mode the value of this in such case will be the global object (window in a browser, we’ll get to it later in the chapter Global object). This is a historical behavior that "use strict" fixes.

Usually such call is a programming error. If there’s this inside a function, it expects to be called in an object context.


# this Alone
* When used alone, this refers to the global object. Because this is running in the global scope. In a browser window the global object is [object Window]:

Eg) 

`let x = this;`

* In strict mode, when used alone, this also refers to the global object:

Eg)

`"use strict";`\
`let x = this;`

# this in a Function (Default)
* In a function, the global object is the default binding for this.
* In a browser window the global object is [object Window]:

Eg)

`function myFunction() {`\
`  return this;`\
`}`

# this in a Function (Strict)
* JavaScript strict mode does not allow default binding.
* So, when used in a function, in strict mode, this is undefined.

Eg)

`"use strict";`\
`function myFunction() {`\
`  return this;`\
`}`

# this in Event Handlers
* In HTML event handlers, this refers to the HTML element that received the event:

Eg) 

`<button onclick="this.style.display='none'">`\
`  Click to Remove Me!`\
`</button>`

# this and object conversion
In non–strict mode, if a function is called with a this value that's not an object, the this value is substituted with an object. null and undefined become globalThis. 

Eg)

`function bar() {`\
`  console.log(Object.prototype.toString.call(this));`\
`}`\

`bar.call(7); // [object Number]`\
`bar.call("foo"); // [object String]`\
`bar.call(undefined); // [object Window]`


# Object Method Binding
In these examples, this is the person object:

Eg)

`const person = {`\
 ` firstName  : "John",`\
 ` lastName   : "Doe",`\
`  id         : 5566,`\
 ` myFunction : function() {`\
`    return this; //[object Object]`\
`  }`\
`};`\
i.e. this.firstName is the firstName property of this (the person object).

# Explicit Function Binding
The call() and apply() methods are predefined JavaScript methods.
They can both be used to call an object method with another object as argument

The example below calls person1.fullName with person2 as an argument, this refers to person2, even if fullName is a method of person1:

Eg)

`const person1 = {`\
`  fullName: function() {`\
 `   return this.firstName + " " + this.lastName;`\
`  }`\
`}`

`const person2 = {`\
`  firstName:"John",`\
`  lastName: "Doe",`\
`}`

`person1.fullName.call(person2); // Return "John Doe":`\

# Function Borrowing
With the bind() method, an object can borrow a method from another object.
This example creates 2 objects (person and member).
The member object borrows the fullname method from the person object:

Eg)

`const person = {`\
`  firstName:"John",`\
`  lastName: "Doe",`\
`  fullName: function () {`\
 `   return this.firstName + " " + this.lastName;`\
`  }`\
`}`

`const member = {`\
`  firstName:"Hege",`\
`  lastName: "Nilsen",`\
`}`

`let fullName = person.fullName.bind(member); //Hege Nilsen`\

# This Precedence
To determine which object this refers to use the following precedence of order.
## Precedence	Object
1	            bind()
2	            apply() and call()
3	            Object method
4	            Global scope

# The bind() method
Calling f.bind(someObject) creates a new function with the same body and scope as f, but the value of this is permanently bound to the first argument of bind, regardless of how the function is being called.

Eg)

`function f() {`\
`  return this.a;`\
`}`\

`const g = f.bind({ a: "azerty" });`\
`console.log(g()); // azerty`

`const h = g.bind({ a: "yoo" }); // bind only works once!`\
`console.log(h()); // azerty`

`const o = { a: 37, f, g, h };`\
`console.log(o.a, o.f(), o.g(), o.h()); // 37 37 azerty azerty`

Eg)

`function makeUser() {`\
`  return {`\
`    name: "John",`\
`    ref: this`\
`  };`\
`}`

`let user = makeUser();`

`alert( user.ref.name ); // Error: Cannot read property 'name' of undefined`

That’s because rules that set this do not look at object definition. Only the moment of call matters. Here the value of this inside makeUser() is undefined, because it is called as a function, not as a method with “dot” syntax.
The value of this is one for the whole function, code blocks and object literals do not affect it. So ref: this actually takes current this of the function.
We can rewrite the function and return the same this with undefined value:

Eg)

`function makeUser(){`\
`  return this; // this time there's no object literal`\
`}`\

`alert( makeUser().name ); // Error: Cannot read property 'name' of undefined`\

As you can see the result of alert( makeUser().name ) is the same as the result of alert( user.ref.name ) from the previous example.
Here’s the opposite case:

Eg)

`function makeUser() {`\
`  return {`\
`    name: "John",`\
`    ref() {`\
`      return this;`\
`    }`\
`  };`\
`}`

`let user = makeUser();`

`alert( user.ref().name ); // John`\
Now it works, because user.ref() is a method. And the value of this is set to the object before dot ..

#### Chaining

Eg)

`  let ladder = {`\
`  step: 0,`\
`  up() {`\
`    this.step++;`\
`  },`\
`  down() {`\
`    this.step--;`\
`  },`\
`  showStep: function() { // shows the current step`\
`    alert( this.step );`\
`  }`\
`};`

Now, if we need to make several calls in sequence, can do it like this:

`ladder.up();`\
`ladder.up();`\
`ladder.down();`\
`ladder.showStep(); // 1`\
`ladder.down();`\
`ladder.showStep(); // 0`

#### Calculator
Eg)

`let calculator = {`\
`sum(){`\
`  return this.a + this.b;`\
`},`\
`mul(){`\
`return this.a * this.b;`\
`},`\
`read() {`\
`    this.a = +prompt('a?', 0);  //The prompt() method displays a dialog box that prompts the user for input.The prompt() method returns the input value if the user clicks "OK", otherwise it returns null. `\
`    this.b = +prompt('b?', 0);`\
`  }`\
`};`\
`calculator.read();`\
`alert( calculator.sum() );`\
`alert( calculator.mul() );`\

Modify the code of up, down and showStep to make the calls chainable, like this:
`ladder.up().up().down().showStep().down().showStep(); // shows 1 then 0`

# Summary 
* In an object method, this refers to the object.
* Alone, this refers to the global object.
* In a function, this refers to the global object.
* In a function, in strict mode, this is undefined.
* In an event, this refers to the element that received the event.
* Methods like call(), apply(), and bind() can refer this to any object.

* Functions that are stored in object properties are called “methods”. 
* Methods allow objects to “act” like object.doSomething().
* Methods can reference the object as this.

The value of this is defined at run-time.
* When a function is declared, it may use this, but that this has no value until the function is called.
* A function can be copied between objects.
* When a function is called in the “method” syntax: object.method(), the value of this during the call is object.

Please note that arrow functions are special: they have no this. When this is accessed inside an arrow function, it is taken from outside.