# What is difference b/w function statement vs function expression vs function declaration?

- 1. Function statememt/ Function Declaration
     Function Statement is also known as a function declaration.

     Eg)

    ` function a(){`\
     `console.log("a is called");`\
     `}`\
     `a();`

- 2. Function expression
     Functon acts like a value. Initializing b with a value. Putting function into b.

     Eg)

     `var b= function (){`\
     `console.log("b is called");` \
    ` }`\
     `b();`

i & ii both are ways of creating a function. Major Difference b/w i & ii is Hoisting.

Eg)

`a(); //Returns function a `\
`b(); // Uncaught TypeError: b is not a function`\
`function a(){`\
`console.log("a is called");`\
`}`

`var b= function(){`\
`console.log("b is called");`  \
`}`

* What if I call this a() and b() even before creating it. During hoisting or memory creation phase a is creating a memory & this function is assigned to a.
* But in case of function expression this b is treated like any other variable. It is assigned undefined initially until the line code hits the line hits itself. So when the JS Engine executes this line by line & it reaches this line then only function is assigned to this variable b until then it is undefined and you cannot call that b.

# What is an anonymous function ?
A function without a name is known as anonymous function. They don't have their own identiy.

Eg)

`function (){`

`}`\
`O/P- // Uncaught SyntaxError: function statements require a function name`

Result out to be Syntax error. Anonymous function looks similar function statement but it has no name. But according to EcmaScript Specification a function statement should always have a name. So this is invalid syntax.

- Use of Anonymous function
  They are used in a place where the functions are used as values. You can use it to assign it to some variable.

  Eg)
  
  `var b= function (){`\
 ` console.log("b is called");  `\
  `}`\
 ` b();`

# Named Function Expression
In function expression instead of using a anonymous function we use a function with a name.

Eg)

`var b= function xyz(){`\
`console.log("b is called");`\
`}`\
`b(); // b is called`\
`xyz(); // Uncaught ReferenceError: xyz is not defined`

This is a corner case. So this xyz() is not created in outer scope, xyz is not a function in Global Space (outer scope). But it is created as a local variable.

Eg)

`var b= function xyz(){`\
`console.log(xyz);  `\
`}`\
`O/P- f xyz(){`\
`console.log(xyz);`\
`}`

# Difference b/w Parameters and Arguments
Lots of programmers use parameters and arguments interchangeably. But they are very different.

Eg)

`var b= function xyz(param1, param2){`\
`console.log("b is called");  `\
`}`\
`b(1,2)`

param1, param2- Parameters/ Identifiers/ Labels. These Identifiers/ Labels that get those values are known as Parameter.This is local variable in the function scope. You can't access param1, param2 outside. These are parameters.
 
b(1,2)- are known as Arguments.
Arguments - The values which we pass inside a function is known as arguments.

# First Class Functon /  First Class Citizens
Instead of b(1,2) these arguments(1,2) we can pass a function inside another functions as Arguments. Functions are treated like values. 

Eg)

`var b= function(param1){`\
`console.log(param1); `\
`}`\
`b(function(){ // Passing a function inside another function`\

`});`\
`O/P- f(){`

`}`

Eg)

`var b= function(param1){`\
`console.log(param1); `\
`}`\
`function xyz(){`\

`};`\
`b(xyz); // Passing a function inside another function`\
`O/P- f(){`
    
`}`

If we pass this function into this function we can also return the function from a function.

Eg)

`var b= function(param1){`\
`return function (){ // Anonymous function was returned from b -> b();`

`}`\
`}`\
`console.log(b());`\
`O/P- f(){`
    
`}`

Eg)

`var b= function(param1){`\
`return function xyz(){ // Anonymous function was returned from b -> b();`

`}`\
`}`\
`console.log(b());`\
`O/P- f xyz(){`
    
`}`

The ability to use functions as values is know as first class function.
Ability of functions to be used as values & assign it to a variable & can be passed as an argument to another functon & can be returned from the function is known as first class function in JS. Functions are first Class Citizens or First Class Functions. 
