# Constructor, operator "new"
The regular {...} syntax allows us to create one object. But often we need to create many similar objects, like multiple users or menu items and so on.
That can be done using constructor functions and the "new" operator.

## Constructor function
Constructor functions technically are regular functions. There are two conventions though:
* They are named with capital letter first.
* They should be executed only with "new" operator.

For instance:
Eg:

`function User(name) {`
 ` this.name = name;`
 ` this.isAdmin = false;`
`}`

`let user = new User("Jack");`

`alert(user.name); // Jack`
`alert(user.isAdmin); // false`

When a function is executed with new, it does the following steps:
1. A new empty object is created and assigned to this.
2. The function body executes. Usually it modifies this, adds new properties to it.
3. The value of this is returned.

In other words, new User(...) does something like:
Eg:
`function User(name) {`
`  // this = {};  (implicitly)`

`  // add properties to this`
`  this.name = name;`
`  this.isAdmin = false;`

`  // return this;  (implicitly)`
`}`

So let user = new User("Jack") gives the same result as:

`let user = {`
`  name: "Jack",`
  `isAdmin: false`
`};`

Now if we want to create other users, we can call new User("Ann"), new User("Alice") and so on. Much shorter than using literals every time, and also easy to read.
That’s the main purpose of constructors – to implement reusable object creation code.

Let’s note once again – technically, any function (except arrow functions, as they don’t have this) can be used as a constructor. It can be run with new, and it will execute the algorithm above. The “capital letter first” is a common agreement, to make it clear that a function is to be run with new

### new function() { … }
If we have many lines of code all about creation of a single complex object, we can wrap them in an immediately called constructor function, like this:
Eg:
// create a function and immediately call it with new
`let user = new function() {`
`  this.name = "John";`
`  this.isAdmin = false;`

  // ...other code for user creation
  // maybe complex logic and statements
  // local variables etc
`};`

This constructor can’t be called again, because it is not saved anywhere, just created and called. So this trick aims to encapsulate the code that constructs the single object, without future reuse.

## Constructor mode test: new.target

The syntax from this section is rarely used, skip it unless you want to know everything.
Inside a function, we can check whether it was called with new or without it, using a special new.target property.
It is undefined for regular calls and equals the function if called with new:
Eg:
`function User() {`
 ` alert(new.target);`
`}`

// without "new":
`User(); // undefined`

// with "new":
`new User(); // function User { ... }`

That can be used inside the function to know whether it was called with new, “in constructor mode”, or without it, “in regular mode”.

We can also make both new and regular calls to do the same, like this:
Eg:
`function User(name) {`
`  if (!new.target) { // if you run me without new`
  `  return new User(name); // ...I will add new for you`
`  }`

`  this.name = name;`
`}`

`let john = User("John"); // redirects call to new User`
`alert(john.name); // John`

This approach is sometimes used in libraries to make the syntax more flexible. So that people may call the function with or without new, and it still works.

Probably not a good thing to use everywhere though, because omitting new makes it a bit less obvious what’s going on. With new we all know that the new object is being created.

## Return from constructors
Usually, constructors do not have a return statement. Their task is to write all necessary stuff into this, and it automatically becomes the result.
But if there is a return statement, then the rule is simple:
> If return is called with an object, then the object is returned instead of this.
> If return is called with a primitive, it’s ignored.

In other words, return with an object returns that object, in all other cases this is returned.

For instance, here return overrides this by returning an object:

Eg:
`function BigUser() {`
`  this.name = "John";`
`  return { name: "Godzilla" };  // <-- returns this object`
`}`

`alert( new BigUser().name );  // Godzilla, got that object`

And here’s an example with an empty return (or we could place a primitive after it, doesn’t matter):

Eg:
`function SmallUser() {`
`  this.name = "John";`
 ` return; // <-- returns this`
`}`

`alert( new SmallUser().name );  // John`

Usually constructors don’t have a return statement. Here we mention the special behavior with returning objects mainly for the sake of completeness.

### Omitting parentheses

By the way, we can omit parentheses after new:
`let user = new User; // <-- no parentheses`
// same as
`let user = new User();`

Omitting parentheses here is not considered a “good style”, but the syntax is permitted by specification.

## Methods in constructor

Using constructor functions to create objects gives a great deal of flexibility. The constructor function may have parameters that define how to construct the object, and what to put in it.

Of course, we can add to this not only properties, but methods as well.

For instance, new User(name) below creates an object with the given name and the method sayHi:

Eg:
`function User(name) {`
  `this.name = name;`

`  this.sayHi = function() {`
   ` alert( "My name is: " + this.name );`
`  };`
`}`

`let john = new User("John");`

`john.sayHi(); // My name is: John`

/*
john = {
   name: "John",
   sayHi: function() { ... }
}
*/

### Summary
* Constructor functions or, briefly, constructors, are regular functions, but there’s a common agreement to name them with capital letter first.
* Constructor functions should only be called using new. Such a call implies a creation of empty this at the start and returning the populated one at the end.

JavaScript provides constructor functions for many built-in language objects: like Date for dates, Set for sets

## Two functions – one object

Is it possible to create functions A and B so that new A() == new B()?

function A() { ... }
function B() { ... }

let a = new A();
let b = new B();

alert( a == b ); // true

If it is, then provide an example of their code.

Yes, it’s possible.
If a function returns an object then new returns it instead of this.

So they can, for instance, return the same externally defined object obj:

`let obj = {};`


`function A() { return obj; }`
`function B() { return obj; }`

`alert( new A() == new B() ); // true`

## Create new Calculator

Create a constructor function Calculator that creates objects with 3 methods:
* read() prompts for two values and saves them as object properties with names a and b respectively.
* sum() returns the sum of these properties.
* mul() returns the multiplication product of these properties.

For instance:
let calculator = new Calculator();
calculator.read();

alert( "Sum=" + calculator.sum() );
alert( "Mul=" + calculator.mul() );

Solution:

`function Calculator(){`
`    this.read= function(){`
  `   this.a= +prompt('a?', 0);`
   ` this.b= +prompt('b?', 0);`
  `  },`
   ` this.sum = function(){`
`        return this.a+ this.b;`
 `   }, `
     ` this.mul = function(){`
  `      return this.a*this.b;`
`    }`
`}`
`let calculator = new Calculator();`
`calculator.read();`

`alert( "Sum=" + calculator.sum() );`
`alert( "Mul=" + calculator.mul() );`

## Create new Accumulator

Create a constructor function Accumulator(startingValue).
Object that it creates should:
* Store the “current value” in the property value. The starting value is set to the argument of the constructor startingValue.
* The read() method should use prompt to read a new number and add it to value.
In other words, the value property is the sum of all user-entered values with the initial value startingValue.
Here’s the demo of the code:

let accumulator = new Accumulator(1); // initial value 1

accumulator.read(); // adds the user-entered value
accumulator.read(); // adds the user-entered value

alert(accumulator.value); // shows the sum of these values

Solution:

`function Accumulator(startingValue){`
   ` this.value = startingValue;`
 `   this.read= function(){`
      `  this.value+ = +prompt('How much to add', 0);`
   ` };`
`}`
`let accumulator = new Accumulator(1);`
`accumulator.read();`
`accumulator.read();`
`alert(accumulator.value);`

# Optional chaining '?.'

The optional chaining ?. is a safe way to access nested object properties, even if an intermediate property doesn’t exist.

## The “non-existing property” problem

If you’ve just started to read the tutorial and learn JavaScript, maybe the problem hasn’t touched you yet, but it’s quite common.

As an example, let’s say we have user objects that hold the information about our users.

Most of our users have addresses in user.address property, with the street user.address.street, but some did not provide them.

In such case, when we attempt to get user.address.street, and the user happens to be without an address, we get an error:

Eg:
`let user = {}; // a user without "address" property`

`alert(user.address.street); // Error!`

That’s the expected result. JavaScript works like this. As user.address is undefined, an attempt to get user.address.street fails with an error.
In many practical cases we’d prefer to get undefined instead of an error here (meaning “no street”).
In Web development, we can get an object that corresponds to a web page element using a special method call, such as document.querySelector('.elem'), and it returns null when there’s no such element.

// document.querySelector('.elem') is null if there's no element
`let html = document.querySelector('.elem').innerHTML; // error if it's null`

Once again, if the element doesn’t exist, we’ll get an error accessing .innerHTML property of null. And in some cases, when the absence of the element is normal, we’d like to avoid the error and just accept html = null as the result.
How can we do this?
The obvious solution would be to check the value using if or the conditional operator ?, before accessing its property, like this:

`let user = {};`

`alert(user.address ? user.address.street : undefined);`

It works, there’s no error… But it’s quite inelegant. As you can see, the "user.address" appears twice in the code.

Here’s how the same would look for document.querySelector:

`let html = document.querySelector('.elem') ? document.querySelector('.elem').innerHTML : null;`

We can see that the element search document.querySelector('.elem') is actually called twice here. Not good.

For more deeply nested properties, it becomes even uglier, as more repetitions are required.

E.g. let’s get user.address.street.name in a similar fashion.

`let user = {}; // user has no address`

`alert(user.address ? user.address.street ? user.address.street.name : null : null);`

That’s just awful, one may even have problems understanding such code.

There’s a little better way to write it, using the && operator:

`let user = {}; // user has no address`

`alert( user.address && user.address.street && user.address.street.name ); // undefined (no error)`

AND’ing the whole path to the property ensures that all components exist (if not, the evaluation stops), but also isn’t ideal.

As you can see, property names are still duplicated in the code. E.g. in the code above, user.address appears three times.
That’s why the optional chaining ?. was added to the language. To solve this problem once and for all!

## Optional chaining

The optional chaining ?. stops the evaluation if the value before ?. is undefined or null and returns undefined.
In other words, value?.prop:
* works as value.prop, if value exists,
* otherwise (when value is undefined/null) it returns undefined.

Here’s the safe way to access user.address.street using ?.:

`let user = {}; // user has no address`

`alert( user?.address?.street ); // undefined (no error)`

The code is short and clean, there’s no duplication at all.

Here’s an example with document.querySelector:

`let html = document.querySelector('.elem')?.innerHTML; // will be undefined, if there's no element`

Reading the address with user?.address works even if user object doesn’t exist:

`let user = null;`

`alert( user?.address ); // undefined`
`alert( user?.address.street ); // undefined`

Please note: the ?. syntax makes optional the value before it, but not any further.

E.g. in user?.address.street.name the ?. allows user to safely be null/undefined (and returns undefined in that case), but that’s only for user. Further properties are accessed in a regular way. If we want some of them to be optional, then we’ll need to replace more . with ?..

### Don’t overuse the optional chaining
We should use ?. only where it’s ok that something doesn’t exist.

For example, if according to our code logic user object must exist, but address is optional, then we should write user.address?.street, but not user?.address?.street.

Then, if user happens to be undefined, we’ll see a programming error about it and fix it. Otherwise, if we overuse ?., coding errors can be silenced where not appropriate, and become more difficult to debug.

### The variable before ?. must be declared

If there’s no variable user at all, then user?.anything triggers an error:
// ReferenceError: user is not defined
`user?.address;`

The variable must be declared (e.g. let/const/var user or as a function parameter). The optional chaining works only for declared variables.

## Short-circuiting
As it was said before, the ?. immediately stops (“short-circuits”) the evaluation if the left part doesn’t exist.
So, if there are any further function calls or operations to the right of ?., they won’t be made.
For instance:

`let user = null;`
`let x = 0;`

`user?.sayHi(x++); // no "user", so the execution doesn't reach sayHi call and x++`

`alert(x); // 0, value not incremented`

## Other variants: ?.(), ?.[]
The optional chaining ?. is not an operator, but a special syntax construct, that also works with functions and square brackets.
For example, ?.() is used to call a function that may not exist.
In the code below, some of our users have admin method, and some don’t:

Eg:
`let userAdmin = {`
 ` admin() {`
   ` alert("I am admin");`
  `}`
`};`

`let userGuest = {};`

`userAdmin.admin?.(); // I am admin`

`userGuest.admin?.(); // nothing happens (no such method)`

Here, in both lines we first use the dot (userAdmin.admin) to get admin property, because we assume that the user object exists, so it’s safe read from it.
Then ?.() checks the left part: if the admin function exists, then it runs (that’s so for userAdmin). Otherwise (for userGuest) the evaluation stops without errors.

The ?.[] syntax also works, if we’d like to use brackets [] to access properties instead of dot .. Similar to previous cases, it allows to safely read a property from an object that may not exist.
Eg:
`let key = "firstName";`

`let user1 = {`
  `firstName: "John"`
`};`

`let user2 = null;`

`alert( user1?.[key] ); // John`
`alert( user2?.[key] ); // undefined`

Also we can use ?. with delete:
`delete user?.name; // delete user.name if user exists`

#### We can use ?. for safe reading and deleting, but not writing
The optional chaining ?. has no use on the left side of an assignment.
Eg:
`let user = null;`

`user?.name = "John"; // Error, doesn't work`
// because it evaluates to: undefined = "John"

## Summary

The optional chaining ?. syntax has three forms:
1. obj?.prop – returns obj.prop if obj exists, otherwise undefined.
2. obj?.[prop] – returns obj[prop] if obj exists, otherwise undefined.
3. obj.method?.() – calls obj.method() if obj.method exists, otherwise returns undefined.

As we can see, all of them are straightforward and simple to use. The ?. checks the left part for null/undefined and allows the evaluation to proceed if it’s not so.
A chain of ?. allows to safely access nested properties.
Still, we should apply ?. carefully, only where it’s acceptable, according to our code logic, that the left part doesn’t exist. So that it won’t hide programming errors from us, if they occur.

# Symbol type
By specification, only two primitive types may serve as object property keys:

* string type, or
* symbol type.

Otherwise, if one uses another type, such as number, it’s autoconverted to string. So that obj[1] is the same as obj["1"], and obj[true] is the same as obj["true"].

## Symbols
A “symbol” represents a unique identifier.

A value of this type can be created using Symbol():

Eg:
`let id = Symbol();`

Upon creation, we can give symbols a description (also called a symbol name), mostly useful for debugging purposes:
// id is a symbol with the description "id"
`let id = Symbol("id");`

Symbols are guaranteed to be unique. Even if we create many symbols with exactly the same description, they are different values. The description is just a label that doesn’t affect anything.

For instance, here are two symbols with the same description – they are not equal:
Eg:
`let id1 = Symbol("id");`
`let id2 = Symbol("id");`

`alert(id1 == id2); // false`

If you are familiar with Ruby or another language that also has some sort of “symbols” – please don’t be misguided. JavaScript symbols are different.
So, to summarize, a symbol is a “primitive unique value” with an optional description. Let’s see where we can use them.

### Symbols don’t auto-convert to a string
Most values in JavaScript support implicit conversion to a string. For instance, we can alert almost any value, and it will work. Symbols are special. They don’t auto-convert.

For instance, this alert will show an error:
`let id = Symbol("id");`
`alert(id); // TypeError: Cannot convert a Symbol value to a string`

That’s a “language guard” against messing up, because strings and symbols are fundamentally different and should not accidentally convert one into another.

If we really want to show a symbol, we need to explicitly call .toString() on it, like here:

`let id = Symbol("id");`
`alert(id.toString()); // Symbol(id), now it works`

Or get symbol.description property to show the description only:

`    let id = Symbol("id");`
`    alert(id.description); // id`

## “Hidden” properties
Symbols allow us to create “hidden” properties of an object, that no other part of code can accidentally access or overwrite.
For instance, if we’re working with user objects, that belong to a third-party code. We’d like to add identifiers to them.
Let’s use a symbol key for it:

Eg:
`let user = { // belongs to another code`
`  name: "John"`
`};`

`let id = Symbol("id");`

`user[id] = 1;`

`alert( user[id] ); // we can access the data using the symbol as the key`

What’s the benefit of using Symbol("id") over a string "id"?

As user objects belong to another codebase, it’s unsafe to add fields to them, since we might affect pre-defined behavior in that other codebase. However, symbols cannot be accessed accidentally. The third-party code won’t be aware of newly defined symbols, so it’s safe to add symbols to the user objects.
Also, imagine that another script wants to have its own identifier inside user, for its own purposes.

Then that script can create its own Symbol("id"), like this:
// ...
`let id = Symbol("id");`

`user[id] = "Their id value";`

There will be no conflict between our and their identifiers, because symbols are always different, even if they have the same name.
…But if we used a string "id" instead of a symbol for the same purpose, then there would be a conflict:

`let user = { name: "John" };`

// Our script uses "id" property
`user.id = "Our id value";`

// ...Another script also wants "id" for its purposes...

`user.id = "Their id value"`
// Boom! overwritten by another script!

## Symbols in an object literal
If we want to use a symbol in an object literal {...}, we need square brackets around it.
Eg:
`let id = Symbol("id");`

`let user = {`
`  name: "John",`
`  [id]: 123 // not "id": 123`
`};`

That’s because we need the value from the variable id as the key, not the string “id”.

## Symbols are skipped by for…in

Symbolic properties do not participate in for..in loop.

Eg:
`let id = Symbol("id");`
`let user = {`
 ` name: "John",`
`  age: 30,`
`  [id]: 123`
`};`

`for (let key in user) alert(key); // name, age (no symbols)`

// the direct access by the symbol works
`alert( "Direct: " + user[id] ); // Direct: 123`

Object.keys(user) also ignores them. That’s a part of the general “hiding symbolic properties” principle. If another script or a library loops over our object, it won’t unexpectedly access a symbolic property.

In contrast, Object.assign copies both string and symbol properties:

Eg:
`let id = Symbol("id");`
`let user = {`
`  [id]: 123`
`};`

`let clone = Object.assign({}, user);`

`alert( clone[id] ); // 123`

There’s no paradox here. That’s by design. The idea is that when we clone an object or merge objects, we usually want all properties to be copied (including symbols like id).

## Global symbols

As we’ve seen, usually all symbols are different, even if they have the same name. But sometimes we want same-named symbols to be same entities. For instance, different parts of our application want to access symbol "id" meaning exactly the same property.

To achieve that, there exists a global symbol registry. We can create symbols in it and access them later, and it guarantees that repeated accesses by the same name return exactly the same symbol.

In order to read (create if absent) a symbol from the registry, use Symbol.for(key).

That call checks the global registry, and if there’s a symbol described as key, then returns it, otherwise creates a new symbol Symbol(key) and stores it in the registry by the given key.

Eg:
// read from the global registry
`let id = Symbol.for("id"); // if the symbol did not exist, it is created`

// read it again (maybe from another part of the code)
`let idAgain = Symbol.for("id");`

// the same symbol
`alert( id === idAgain ); // true`

Symbols inside the registry are called global symbols. If we want an application-wide symbol, accessible everywhere in the code – that’s what they are for.

## Symbol.keyFor

We have seen that for global symbols, Symbol.for(key) returns a symbol by name. To do the opposite – return a name by global symbol – we can use: Symbol.keyFor(sym):
Eg:
// get symbol by name
`let sym = Symbol.for("name");`
`let sym2 = Symbol.for("id");`

// get name by symbol
`alert( Symbol.keyFor(sym) ); // name`
`alert( Symbol.keyFor(sym2) ); // id`

The Symbol.keyFor internally uses the global symbol registry to look up the key for the symbol. So it doesn’t work for non-global symbols. If the symbol is not global, it won’t be able to find it and returns undefined.

That said, all symbols have the description property.
Eg:
`let globalSymbol = Symbol.for("name");`
`let localSymbol = Symbol("name");`

`alert( Symbol.keyFor(globalSymbol) ); // name, global symbol`
`alert( Symbol.keyFor(localSymbol) ); // undefined, not global`

`alert( localSymbol.description ); // name`

## System symbols
There exist many “system” symbols that JavaScript uses internally, and we can use them to fine-tune various aspects of our objects.

They are listed in the specification in the Well-known symbols table:

* Symbol.hasInstance
* Symbol.isConcatSpreadable
* Symbol.iterator
* Symbol.toPrimitive

### Summary

Symbol is a primitive type for unique identifiers.
Symbols are created with Symbol() call with an optional description (name).

Symbols are always different values, even if they have the same name. If we want same-named symbols to be equal, then we should use the global registry: Symbol.for(key) returns (creates if needed) a global symbol with key as the name. Multiple calls of Symbol.for with the same key return exactly the same symbol.

Symbols have two main use cases:
1. “Hidden” object properties.

If we want to add a property into an object that “belongs” to another script or a library, we can create a symbol and use it as a property key. A symbolic property does not appear in for..in, so it won’t be accidentally processed together with other properties. Also it won’t be accessed directly, because another script does not have our symbol. So the property will be protected from accidental use or overwrite.
So we can “covertly” hide something into objects that we need, but others should not see, using symbolic properties.
2. There are many system symbols used by JavaScript which are accessible as Symbol.*. We can use them to alter some built-in behaviors. We’ll use Symbol.iterator for iterables, Symbol.toPrimitive to setup object-to-primitive conversion and so on.

Technically, symbols are not 100% hidden. There is a built-in method Object.getOwnPropertySymbols(obj) that allows us to get all symbols. Also there is a method named Reflect.ownKeys(obj) that returns all keys of an object including symbolic ones. But most libraries, built-in functions and syntax constructs don’t use these methods.

# Object to primitive conversion

What happens when objects are added obj1 + obj2, subtracted obj1 - obj2 or printed using alert(obj)?
JavaScript doesn’t allow you to customize how operators work on objects. 
In case of such operations, objects are auto-converted to primitives, and then the operation is carried out over these primitives and results in a primitive value.

That’s an important limitation: the result of obj1 + obj2 (or another math operation) can’t be another object!
E.g. we can’t make objects representing vectors or matrices (or achievements or whatever), add them and expect a “summed” object as the result. Such architectural feats are automatically “off the board”.
So, because we can’t technically do much here, there’s no maths with objects in real projects. When it happens, with rare exceptions, it’s because of a coding mistake.
we’ll cover how an object converts to primitive and how to customize it.
We have two purposes:
1. It will allow us to understand what’s going on in case of coding mistakes, when such an operation happened accidentally.
2. There are exceptions, where such operations are possible and look good. E.g. subtracting or comparing dates (Date objects). 

## Conversion rules
1. There’s no conversion to boolean. All objects are true in a boolean context, as simple as that. There exist only numeric and string conversions.
2. The numeric conversion happens when we subtract objects or apply mathematical functions. For instance, Date objects (to be covered in the chapter Date and time) can be subtracted, and the result of date1 - date2 is the time difference between two dates.
3. As for the string conversion – it usually happens when we output an object with alert(obj) 

## Hints
How does JavaScript decide which conversion to apply?

There are three variants of type conversion, that happen in various situations. They’re called “hints”, as described in the specification:

"string"
For an object-to-string conversion, when we’re doing an operation on an object that expects a string, like alert:

Eg:
`// output`
`alert(obj);`

`// using object as a property key`
`anotherObj[obj] = 123;`

"number"
For an object-to-number conversion, like when we’re doing maths:

Eg:
// explicit conversion
`let num = Number(obj);`

// maths (except binary plus)
`let n = +obj; // unary plus`
`let delta = date1 - date2;`

// less/greater comparison
`let greater = user1 > user2;`

"default"
Occurs in rare cases when the operator is “not sure” what type to expect.
For instance, binary plus + can work both with strings (concatenates them) and numbers (adds them). So if a binary plus gets an object as an argument, it uses the "default" hint to convert it.

Also, if an object is compared using == with a string, number or a symbol, it’s also unclear which conversion should be done, so the "default" hint is used.

Eg:
// binary plus uses the "default" hint
`let total = obj1 + obj2;`

// obj == number uses the "default" hint
`if (user == 1) { ... };`

The greater and less comparison operators, such as < >, can work with both strings and numbers too. Still, they use the "number" hint, not "default". That’s for historical reasons.
In practice though, things are a bit simpler.
All built-in objects except for one case (Date object, we’ll learn it later) implement "default" conversion the same way as "number". And we probably should do the same.
Still, it’s important to know about all 3 hints, soon we’ll see why.
To do the conversion, JavaScript tries to find and call three object methods:

1) Call obj[Symbol.toPrimitive](hint) – the method with the symbolic key Symbol.toPrimitive (system symbol), if such method exists,
2) Otherwise if hint is "string"
try calling obj.toString() or obj.valueOf(), whatever exists.
3) Otherwise if hint is "number" or "default"
try calling obj.valueOf() or obj.toString(), whatever exists.

## Symbol.toPrimitive

Let’s start from the first method. There’s a built-in symbol named Symbol.toPrimitive that should be used to name the conversion method, like this:
Eg:
`obj[Symbol.toPrimitive] = function(hint) {`
  // here goes the code to convert this object to a primitive
  // it must return a primitive value
  // hint = one of "string", "number", "default"
`};`

If the method Symbol.toPrimitive exists, it’s used for all hints, and no more methods are needed.

For instance, here user object implements it:

Eg:
`let user = {`
 ` name: "John",`
`  money: 1000,`

`  [Symbol.toPrimitive](hint) {`
`    alert(`hint: ${hint}`);`
 `   return hint == "string" ? `{name: "${this.name}"}` : this.money;`
 ` }`
`};`

// conversions demo:
`alert(user); // hint: string -> {name: "John"}`
`alert(+user); // hint: number -> 1000`
`alert(user + 500); // hint: default -> 1500`

As we can see from the code, user becomes a self-descriptive string or a money amount, depending on the conversion. The single method user[Symbol.toPrimitive] handles all conversion cases.

## toString/valueOf
If there’s no Symbol.toPrimitive then JavaScript tries to find methods toString and valueOf:

1) For the "string" hint: call toString method, and if it doesn’t exist or if it returns an object instead of a primitive value, then call valueOf (so toString has the priority for string conversions).
2) For other hints: call valueOf, and if it doesn’t exist or if it returns an object instead of a primitive value, then call toString (so valueOf has the priority for maths).

Methods toString and valueOf come from ancient times. They are not symbols (symbols did not exist that long ago), but rather “regular” string-named methods. They provide an alternative “old-style” way to implement the conversion.

These methods must return a primitive value. If toString or valueOf returns an object, then it’s ignored (same as if there were no method).

By default, a plain object has following toString and valueOf methods:

The toString method returns a string "[object Object]".
The valueOf method returns the object itself.

Eg: 
`let user = {name: "John"};`

`alert(user); // [object Object]`
`alert(user.valueOf() === user); // true`

So if we try to use an object as a string, like in an alert or so, then by default we see [object Object].

The default valueOf is mentioned here only for the sake of completeness, to avoid any confusion. As you can see, it returns the object itself, and so is ignored. 

Let’s implement these methods to customize the conversion.

Eg:
`let user = {`
 ` name: "John",`
 ` money: 1000,`

  // for hint="string"
`  toString() {`
`    return `{name: "${this.name}"}`;`
`  },`

  // for hint="number" or "default"
`  valueOf() {`
   ` return this.money;`
 ` }`

`};`

`alert(user); // toString -> {name: "John"}`
`alert(+user); // valueOf -> 1000`
`alert(user + 500); // valueOf -> 1500`

As we can see, the behavior is the same as the previous example with Symbol.toPrimitive.

Often we want a single “catch-all” place to handle all primitive conversions. In this case, we can implement toString only, like this:

Eg:
`let user = {`
`  name: "John",`

`  toString() {`
  `  return this.name;`
`  }`
`};`

`alert(user); // toString -> John`
`alert(user + 500); // toString -> John500`

In the absence of Symbol.toPrimitive and valueOf, toString will handle all primitive conversions.

## A conversion can return any primitive type

The important thing to know about all primitive-conversion methods is that they do not necessarily return the “hinted” primitive.

There is no control whether toString returns exactly a string, or whether Symbol.toPrimitive method returns a number for the hint "number".

The only mandatory thing: these methods must return a primitive, not an object.

if toString or valueOf returns an object, there’s no error, but such value is ignored (like if the method didn’t exist). 
In contrast, Symbol.toPrimitive is stricter, it must return a primitive, otherwise there will be an error.

## Further conversions
As we know already, many operators and functions perform type conversions, e.g. multiplication * converts operands to numbers.
If we pass an object as an argument, then there are two stages of calculations:

1. The object is converted to a primitive (using the rules described above).
2. If necessary for further calculations, the resulting primitive is also converted.

Eg:
`let obj = {`
  // toString handles all conversions in the absence of other methods
`  toString() {`
 `   return "2";`
  `}`
`};`

`alert(obj * 2); // 4, object converted to primitive "2", then multiplication made it a number`

1. The multiplication obj * 2 first converts the object to primitive (that’s a string "2").
2. Then "2" * 2 becomes 2 * 2 (the string is converted to number).

Binary plus will concatenate strings in the same situation, as it gladly accepts a string:

`let obj = {`
`  toString() {`
  `  return "2";`
 ` }`
`};`

`alert(obj + 2); // 22 ("2" + 2), conversion to primitive returned a string => concatenation`

# Summary
The object-to-primitive conversion is called automatically by many built-in functions and operators that expect a primitive as a value.

There are 3 types (hints) of it:

* "string" (for alert and other operations that need a string)
* "number" (for maths)
* "default" (few operators, usually objects implement it the same way as "number")

The specification describes explicitly which operator uses which hint.

1. Call obj[Symbol.toPrimitive](hint) if the method exists,
2. Otherwise if hint is "string" try calling obj.toString() or obj.valueOf(), whatever exists.
3. Otherwise if hint is "number" or "default" try calling obj.valueOf() or obj.toString(), whatever exists.

All these methods must return a primitive to work (if defined).

In practice, it’s often enough to implement only obj.toString() as a “catch-all” method for string conversions that should return a “human-readable” representation of an object, for logging or debugging purposes.