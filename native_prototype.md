# Native prototypes

The "prototype" property is widely used by the core of JavaScript itself. All built-in constructor functions use it.

## Object.prototype

Let’s say we output an empty object:

let obj = {};
alert( obj ); // "[object Object]" ?

Where’s the code that generates the string "[object Object]"? That’s a built-in toString method, but where is it? The obj is empty!

…But the short notation obj = {} is the same as obj = new Object(), where Object is a built-in object constructor function, with its own prototype referencing a huge object with toString and other methods.

Here’s what’s going on:

![user reference](images/object-prototype.svg "reference")

When new Object() is called (or a literal object {...} is created), the [[Prototype]] of it is set to Object.prototype according to the rule that we discussed in the previous chapter:
![user reference](images/object-prototype-1.svg "reference")

So then when obj.toString() is called the method is taken from Object.prototype.

We can check it like this:

let obj = {};

alert(obj.__proto__ === Object.prototype); // true

alert(obj.toString === obj.__proto__.toString); //true
alert(obj.toString === Object.prototype.toString); //true
