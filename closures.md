# How JS works and Execution Context?

- Everything in JS happens inside the execution context. Assume it to be a big box or container in which whole JS code is executed.

It has 2 components in it.

- Variable Environment and
- Thread of Execution

* Variable Environment- This a place where all the variables and functions are stored as key value pairs.
  It is a sort of environment in which all the variables and functions are stored as key value pairs.
  It is also known as memory component.

* Thread of Execution- This is the place where code is executed one line at a time. It is also known as thread of
  execution just like a thread in which whole code is executed one line at a time.

- Javascript is a synchronous single threaded language. Single threaded means JS can only execute one command at a time.
- Synchronous single threaded means JS can only execute one command at a time and in a specific order. So it means that
  it can only go to the next line once the current line has been finished executing.

# What happens when you run JS code ?

- An execution context is created. When you run this code a Global Execution Context is created in two phases
- Creation Phase (Memory Creation Phase)
- Code Execution Phase

Eg:

`var n=5;` \
`function cube(num){` \
`var ans=num*num;` \
`return ans;` \
`}` \
`var cube2=cube(n);` \
`var cube4=cube(4);`

- So if you have this code, in first phase of memory creation JS will allocate memory to all the variables and functions.
- So now let me tell you what does it store when it allocates memory to n it stores a special value which is known as
- undefined. In case of function it literally stores the whole code of functions inside this memory space.

- In the first phase, JS skims through whole program line by line and it allocates memory to all the variables and functions as soon as it encounter variable it reserves memory for it and allocated a special value undefined to it and in case of function this whole function code is literally(you can assume to be) copied over here in the memory space.

- Second Phase is code execution phase so let see how this code is executed after the memory allocation. So JS once again
  runs through this whole JS program line by line and it executes the code now. So this is the point when all these
  functions & every calculation in the program is done.

- So till now value was undefined. Now in the second phase of creation of Execution Context i.e Code Execution Phase. Now the actual value of the variable is now being placed actually in this placeholder or the identifier (n). Here we do a function invocation. Whenever you see a function name with these parentheses it means that the function is now being executed. Function over here are like a mini program and whenever a new function is invoked this mini program
  is invoked an altogether a new Execution Context is created.

- When you run a function or invoke a function a brand new Execution Context is created.

- num over here in the code is known as the parameter of the function and n over here is the argument.

* Return keyword tells this function that you are done with your work now just return the control back to the execution
  context where the function was invoked.

- As soon as we encounter this return statement we have this return of ans so it finds the value of ans inside this local
  memory. Memory Component is the location memory inside the code execution phase.It returns the control back to cube2.
  Undefined replaced by 4.

- One more thing which happens when the whole function is executed is that the whole execution context for that instance
  will be deleted. So now there won't be the execution context as soon as we return the value. so this whole thing will be
  deleted.This is how whole code is executed.

# Call Stack

- Call Stack - It handles everything to manage this execution context creation, deletion, and the control. It manages a Stack. It has it's own call stack. Call stack maintains the order of execution of execution contexts.

* It is a Stack & everytime in the bottom of the stack we have our global execution context. Thats means whenever any JS
  program is run, this call stack is populateed with this global execution context. The whole execution context is pushed
  inside this stack.

* Whenever a function is invoked,a new execution context is created. This execution context is put inside the stack. Once we are done with executing this function, we return and now this execution context is moved out or popped out of the
  stack and control goes back to the global execution context, where it is left. So this call stack is only for managing
  these execution contexts.

* Where an execution context is created it is pushed into the stack & whenever an execution context is deleted it will move out of the stack. So this is how whole control is being managed. The whole execution context deletion everything is managed bu JS.

* After the whole thing is executed, the global execution context,the call stack gets empty. The global execution context is also gone from the call stack & we are done with our JS program. So that is how the whole code inside the JS engine is
  executed.

* Call stack is also known by using very fancy names.There are lot of fancy names which you see on web.

- Execution Context Stack
- Program Stack
- Control Stack
- Runtime Stack
- Machine Stack

# Hoisting in JS (variables and functions)

- Hoisting is a phenomena in JS, by which we can access variables and functions even before we have initialized it or have put some values in it. You can access it without any error.

Eg)

`getColor(); // blue`\
`console.log(x); //undefined`\
`var x=7;`

`function getColor() {`\
`console.log("blue);`\
`}`\
`console.log(x); //7`\
`getColor(); // blue`\
`console.log(getColor); //Return the above function`

- Even before code starts executing memory is allocated to all variables and functions.

# Difference Betweeen not defined and undefined?

Eg)

`getColor(); // blue `
`console.log(x); // Uncaught ReferenceError :x is not defined` \
`//var x=7 was removed`

`function getColor() {` \
`console.log("blue);` \
`}` \
`var x=7;`

- As soon as it sees x we have not reserved the memory for x, we just have reserved memory for getColor. Now x is not
  present and there is no value for x, so now we get a reference error.

- Not defined is something which has not been defined memory.
  undefined!==empty

* undefined is a special keyword, its takes up its own memory. It is like a placeholder which is kept for timebeing until a variable is assigned some other value. Till that time it will store the placeholder known as undefined.

- JS is a loosely typed language. It does not attaches it variable to any specific data type. Suppose if we created "a" and put in String in it. Later on we can add numbers, booleans in it. JS is very flexible that is why it is loosely typed
  language or weakly typed language.

## Arrow function

In case of Arrow function it just behaves like the variables. Now getColor() does not behave like the functions it behaves like the variables. In the memory allocation phase of the execution context, just like it allocates undefined to x.

- It defines undefined to getColor.

Eg)

`getColor();`\
`console.log(getColor);`\
`var getColor=()=>{`\
`console.log("blue";)`\
`}`

## Again if we delare functions in this way it will bahave like a variables.

Eg)

`getColor1(); // getColor1: undefined`\
`var getColor1= function (){`\
`console.log("Red");`\
`}`

# Shortest JS program and this keyword

- window - is a big Object with lot of functions and variables. They are created by the JavaScript Engine into global scope.
- Global Object is created along with globaL execution context.
- this- At the global level this points to window object.

* A global Object in case of browsers it is known as window.

#### this- At the global level this points to window object.

- JS runs on server. Wherever JS is running there must be a JS engine. All JS engine have the responsibility to create this global object for browsers its window. But there is always a global object created. At the global/ base level in the
  global execution context this points to window object.

#### this===window o/p-true

- Whenever we create a Execution Context this is created with it. Even for the function a this is created with it. Even for a global execution context this is created with it.

## If we try to put any variables/function in global space?

- Global space is nothing but any code we write in JS which is not inside the function. Anything which is not inside the
  function it is global space. The variables and functions get attached to the global object(window)

- so console.log(window.a)=== console.log(a)=== console.log(this.a)

## The Scope Chain, Scope and Lexical Environment?

- Scope is directly related to the lexical environment. Scope, Scope chain and closures depend on lexical environment.

Eg)

`function a(){`\
`console.log(b);`\
`}`\
`var b=10;`\
`a();`

`O/P= 10`

## Whenever it reaches & it tries to execute the console.log(b). What will JS do ?

JS will try to find out whether b exist in local memory space or not. JS will try to find inside this local memory of
this a's execution context. This function a's local memory it will try to find out b & it won't be there because we never
created b inside that function.

# So what will happen? Will it print undefined?

Eg)

`function a(){`\
`c();`\
`function c(){`\
`console.log(b);`\
`}`\
`}`\
`var b=10;`\
`a();`

`O/P= 10`

Eg)

`function a(){`\
`var b=10;`\
`function c(){`\
`console.log(b);`\
`}`\
`}`\
`a();`

`O/P= 10`

Eg)

`function a(){`\
`var b=10;`\
`c();`\
`function c(){`

`}`\
`}`\
`a();`\
`console.log(b);`

`O/P= undefined`

### Scope means where you can access a specific variable or function in our code. 2 scopes of a variable

##### What is the scope of this variable b? Where can i access this variable b?

##### Is b inside the scope of function c()? Can I access this b inside c()?

- Scope is directly dependent on the lexical environment.

* So whenever an execution context is created a lexical environment is created. Lexical environment is the (local memory)
  along with the lexical environment of its parent. Lexical means inherky or in order or in sequence. Function c() is
  lexically sitting inside a()function. In code terms where that specific code is present physically inside a and a
  is present inside Global Scope. Lexical environment of Global Parent is null. A lexical environment is part of every
  execution context (stack frame) and is a map between identifiers (i.e. local variable names) and values.

# Scope Chain-

- Scope Chain- The mechanism of finding these variables first in local memory and didn't found them it went to the reference of the outer parent. This way of finding is known as Scope Chain. Its nothing but the chain of all these lexical environment & parent references. This is what is scope chain. It defines whether a variable function is inside the scope or not. If it is not found then it is not inside the scope chain.

## let and const in JS

- let and const declarations are hoisted

Eg)

`console.log(b); //undefined`\
`console.log(a); //ReferenceError:cannot access 'a' before initialization`\
`let a=10;`\
`var b=100;`\
`console.log(a); //10`\
`console.log(x); //ReferenceError: x is not defined`

- Memory was assigned to b to the var initialization. Var b was attached to the Global object.

- But in case of let and const they are also allocated memory and that is called hoisting. They are stored in different memory space than Global. They are not on a global object now. They are stored on separate memory space and we cannot access these memory space.

## Temporal Dead Zone(TDZ)

- Temporal Dead Zone(TDZ)- is the time since when this let variable was hoisted & till it is initialized some value. The time between that is known as Temporal Dead Zone.

* window and this cannot be used to access let variables like the var variable.

- let- let is more strict than var.Redeclaration is not possible in let. Duplication of let is not possible. Same name cannot be used to declare var variables.

Eg)

`let a=10;`\
`let a =100; //Syntax error:Identifier has already been declared`

Eg)

`let a=10;`\
`var a =100; //Syntax error:Identifier has already been declared`

- Const- behaves similar to let but is more stricter than let.

Eg)

`let a;`\
`a=10;`\
`console.log(a);// 10`
Valid and possible in let

Eg)\

`const a;`\
`a=10;`\
`console.log(a);// Syntax error:Missing Intializer in const declaration`\

Eg)

`const a=1000;// only possible`

Eg)

`const a=1000;`\
`a=6; // Type error:Assignment to constant variable.`

# Difference between reference error and syntax error and type error.

- Reference Error: When JS engine tries to find a specific variable inside the memory space & you cannot access it.
  Eg)\

`console.log(a); //TDZ`\
 `let a=1000;`\
`console.log(x);`

- Syntax Error:Here we are missing syntax.

Eg)

`let a=100;`\
`let a=50; //a cannot be redeclared. Redeclaration is not possible`

- Type Error: You are trying to assign any other variable to the constant variable. Variable b is of const type

Eg)

`const a=1000;`\
`a=6; // Type error:Assignment to constant variable`

- Try to use const whenever possible. If not const then use let. Try to keep var aside and donot use very much. To avoid Temporal Dead Zone to always put declarations and initializations on top of the scope. To avoid unexpected errors in JS code. We are shrinking this temporal dead zone window to zero while moving our initialization on the top.

# Block Scope and Shadowing in JS

## What is block in JS?

- A block is defined by {}. A block is known as compound statement. A block is use to combine multiple JS statement
  into one group. We need to group these statements together so we can use multiple statements in a place where JS expects
  only one statement.

Eg)

`{`\
//compound statement
`var a=10;`\
`console.log(a);`\
`}`

Eg)

`if(true);//SyntaxError: Unexpected end of Input`\
it expects one statement over here that why we get this error.

Eg)

`if(true)true;`

Eg)

`if(true){`\
//compound statement
`var a=10;`\
`console.log(a);`\
`}`

- Blocked Scope: what all variables and functions we can access inside this block.

Eg)

`{`\
`var a=10;`\
`let b=20;`\
`const c=30;`\
`}`

Block- a  
Script- b,c

- We get let and const inside the block scope. This is a separate space where these b & c are hoisted & assigned undefined.

- let and const are blocked scope i.e they are stored in the separate memory space which is reserved for this block & once it is finished executing this block & these variables let and const are no longer accessible. You cannot access let and const outside these block. var is accessible outside as it is in the global scope.

Eg)

`{`\
`var a=10;`\
`let b=20;`\
`const c=30;`\
`console.log(a); //10`\
`console.log(b); //20`\
`console.log(c); //30`\
`}`

`console.log(a); //10`\
`console.log(b); //ReferenceError:b is not defined`\
`console.log(c); //ReferenceError:b is not defined`

- Block scope is removed only Global is present.

## Shadowing in JS

Eg)

`var a=100; q`\
`{`\
`var a=10; v`\
`let b=20;`\
`const c=30;`\
`console.log(a); //10`\
`console.log(b); //20`\
`console.log(c); //30`\
`}`\
`console.log(a); //10`

- If we have a same name variable outside this block so v variable shadows q variable. So v variable was shadowed & it also modified the value of q bcoz they are pointing to same memory location.

## let in Shadowing

Eg)

`let b=100; q`\
`{`\
`var a=10;  `\
`let b=20; v`\
`const c=30;`\
`console.log(a); //10`\
`console.log(b); //20`\
`console.log(c); //30`\
`}`\
`console.log(b); //100`

Global- a:10
Block- b:20 ,c:30  
Script- b:100

This b of v shadowed over q. But if we access b outside I get q b's value which is outside the block.

## const in Shadowing

Eg)

`const c=50; q`\
`{`\
`var a=10;  `\
`let b=20;  `\
`const c=30; v`\
`console.log(a); //10`\
`console.log(b); //20`\
`console.log(c); //30`\
`}`\
`console.log(c); //50`

Global- a:10
Block- b:20 ,c:30  
Script- b:50

This c of v shadowed over q. But if we access b outside I get q c's value which is outside the block.

## Shadowing in functions

- Shadowing is not only concept of block even if it behaves same in functions, also Similar Functional Scope Shadowing
  is similar.

Eg)

`const c=100;`\
`function x(){`\
`const c=30;`\
`console.log(c); //30`\
`}`\
`console.log(c); //100`\
`x();`

## Illegal Shadowing

Eg) Allowed

`var a= 20;`\
`{`\
`var a= 20;`\
`}`

Eg) Not Allowed

`let a= 20;`\
`{`\
`var a= 20;`\
`}`

- O/P- SyntaxError: Identifier a has alraedy been declared. Illegal Shadowing let using var is not possible. If it is
  shadowing something it shoul not cross boundary of its scope. In a particular scope let cannot be redeclared.

Eg) Allowed

`let a= 20;`\
`{`\
`let a= 20;`\
`}`

Eg) Allowed

`var a= 20;`\
`{`\
`let a= 20;`\
`}`

Eg) Allowed

`const a= 20;`\
`{`\
`const a= 20;`\
`}`

Eg) Allowed

`let a= 20;`\
`function x(){`\
`var a=20;`\
`}`

- This is not illegal shadowing. It won't give any error. As because var is functional scope so var is inside it boundaries. It is not interferring with outside a now.

* Block scope also follows lexical scope. These scope are lexical present.

Eg) Allowed

`const a= 20;`\
`{`\
`const a=100; ` \
` {`\
`const a=200;  `\
` console.log(a); //200`\
`}`\
`console.log(a); //100`\
`}`\
`console.log(a);//20`
Block- a:200
Block- a:100  
Script- a:20

- a will get access from its nearest a.

Eg) Allowed

`const a= 20;`\
`{`\
`const a=100; b`\
`{ g`\
`console.log(a); //100`\
`}`\
`console.log(a); //100`\
`}`\
`console.log(a);//20`

- It follows lexical scope chain pattern if it was not present g scope a value will be from b scope 100.

## Arrow function Scope

Whenever we declare a function with a function keyword or arrow function. We feel those might have different scope but
they are exactly same.

# Prerequisites of Closure

You should have a good understanding of the following topics to understand closure:

- How JavaScript's execution context works?
- How memory allocation takes place?
- Lexical Environment
- Variable Scope in JavaScript
- Scope Chain
- Scope
- Hoisting in JS
- Block scope
- Shadowing in JavaScript
- Understanding this keyword

# Closures in JS

- Closures basically means that a function bind together with its lexical environment.
- Function bundled with its lexical environment.
- Function along with lexical scope together bundled is known as Closure.
- Function along with its lexical scope is a closure function.
- A function along with the reference to its outer environment together forms a closure.
- In other words, Closure is a combination of a function and its lexical scope bundled together forms a closure.
- Each and every function in JS has access to its outer lexical environment that means like access to its variables &
  functions which is present in the environment of its parent so each and every function has access to them. So even when this function is like executed in some other scope not its original scope but even when it is executedin some other scope. It still remembers its outer lexical environment where it was originally present in the code. So this what closure is.
- A closure is a pairing of a function and a reference to that function's outer scope (lexical environment)

- Every function in JavaScript maintains a reference to its outer lexical environment. This reference is used to
  configure the execution context created when a function is invoked. This reference enables code inside the function
  to "see" variables declared outside the function, regardless of when and where the function is called.

- Every function in JavaScript maintains a link to its outer lexical environment. A lexical environment is a map of all
  the names (eg. variables, parameters) within a scope, with their values.

- So, whenever you see the function keyword, code inside that function has access to variables declared outside the function.

Eg)

`function foo(x) {`\
`var tmp = 3;`\
`function bar(y) {`\
`console.log(x + y + (++tmp));`\
`}`\
`bar(10);`\
`}`\
`foo(2);`\
`O/P- 16`

- This will log 16 because function bar closes over the parameter x and the variable tmp, both of which exist in the
  lexical environment of outer function foo. Function bar, together with its link with the lexical environment of function foo is a closure.

- A function doesn't have to return in order to create a closure. Simply by virtue of its declaration, every function
  closes over its enclosing lexical environment, forming a closure.

Eg)

`function foo(x) {`\
`var tmp = 3;`\
`return function (y) {`\
`console.log(x + y + (++tmp)); // will also log 16`\
`}`\
`}`

`var bar = foo(2);`\
`bar(10); // 16`\
`bar(10); // 17`

- The above function will also log 16, because the code inside bar can still refer to argument x and variable tmp, even
  though they are no longer directly in scope.

- However, since tmp is still hanging around inside bar's closure, it is available to be incremented. It will be
  incremented each time you call bar.
- The simplest example of a closure is this:

  Eg)

`  var a = 10;`\
`function test() {`\
`console.log(a); // will output 10`\
`console.log(b); // will output 6`\
`}`\
`var b = 6;`\
`test();`

- When a JavaScript function is invoked, a new execution context ec is created. Together with the function arguments and
  the target object, this execution context also receives a link to the lexical environment of the calling execution
  context, meaning the variables declared in the outer lexical environment (in the above example, both a and b) are
  available from ec.
- Every function creates a closure because every function has a link to its outer lexical environment.
- Note that variables themselves are visible from within a closure, not copies.

Eg) If you say to your child, "Go to the kitchen and get me a trash bag" they will be able to remember that direction.
We can use this example to explain closures, as follows:

- The kitchen is a closure that has a local variable, called trashBags. There is a function inside the kitchen called
  getTrashBag that gets one trash bag and returns it.

`function makeKitchen() {`\
`var trashBags = ['A', 'B', 'C']; // only 3 at first`\
`return {`\
`getTrashBag: function() {`\
`return trashBags.pop();`\
`}`\
`};`\
`}`

`var kitchen = makeKitchen();`

`console.log(kitchen.getTrashBag()); // returns trash bag C`\
`console.log(kitchen.getTrashBag()); // returns trash bag B`\
`console.log(kitchen.getTrashBag()); // returns trash bag A`

- Each time makeKitchen() is called, a new closure is created with its own separate trashBags.
- The trashBags variable is local to the inside of each kitchen and is not accessible outside, but the inner function
  on the getTrashBag property does have access to it.
- Every function call creates a closure, but there would be no need to keep the closure around unless an inner function,
  which has access to the inside of the closure, can be called from outside the closure. Returning the object with the
  getTrashBag function does that here.

Eg)

`function x(){`\
`var a=7;`\
`function y(){`\
`console.log(a);`\
`}`\
`y();`\
`}`\
`x();`

- Inside y it forms a closure with the variable which were part of x lexical scope. Function y was bind to the variables of function x.

Eg)

`function x(){`\
`var a= function y(){`\
`console.log(a);`\
`}`\
`y();`\
`}`\
`x();`

Eg)

`function x(){`\
`var a= 7;`\
`y();`\
`}`\
`x(function y(){`\
`console.log(a);`\
`});`

Eg)

`function x(){`\
`var a= 7;`\
`function y(){`\
`console.log(a);`\
`}`\
`return y;`\
`}`\
`x();`

- Return these functions from function. You can return these function out of function. Instead of calling you can also
  return these function.

- y is the whole function code. So when you return it what its return is. Once we invoke this x it returns y & it returns
  this y to the place where function was invoked when you execute x() it will return y.

Eg)

`function x(){`\
`var a= 7;`\
`function y(){`\
`console.log(a);`\
`}`\
`return y;`\
`}`\
`var z=x();`\
`console.log(z);`\
`z();`\
`O/P=7`

- It returns whole function y. x was like vanished. JS is synchronous like after returning this. x is gone and no
  longer in call Stack. x execution context is no longer in the call stack. It completely gone all the variables & functions
  are gone. Now z contains this function y.

- Here comes closures into picture. Functions are so beautiful when they are returned from another function they still
  maintain there lexical scope. They remember where they were actually present. Though x is no longer exist. But still y()
  remembers its lexical scope where it came from. It remember there was something known as a.
  So when we return y not just function code was returned but the closure was returned.

Eg)

`function x(){`\
`var a= 7;`\
`return function y(){`\
`console.log(a);`\
`}`\
`}`\
`var z=x();`\
`console.log(z);`\
`z();`\
`O/P=7`

Eg)

`function x(){` \
`var a= 7;`\
`function y(){`\
`console.log(a);`\
`}`\
`a=100;`\
`return y;`\
`}`\
`var z=x();`\
`console.log(z); //100`\
`z();`

- a does not refer to its value a's reference bit is returned.

Eg)

`function z(){`\
`var b=100;`\
`function x(){`\
`var a= 7;`\
`function y(){`\
`console.log(a,b);`\
`}`\
`y(); `\
`}`\
`x();`\
`}`\
`z();`\
`O/P=7,100`

Eg)

`function outer(){`\
`var a=10;`\
`function inner(){`\
`console.log(a);`\
`}`\
`return inner;`\
`}`\
`outer()();`\
`O/P-10`

- This inner function has access to its outer environment. So if I create a variable in its outer environment. So this inner function has access to this "a" variable. This inner function along with its lexical outer environment is known as Closure.
- This inner function forms a closure with this outer environment, environement of its parents & whatever it has access to.The major part of closure is if I return this inner function also if I try to return this inner function outside from this function & try to access it somewhere else in the outer scope also. It still remember what the value of "a" was.
- So suppose if I do a outer()(). I run this code it still returns 10. Even if it is executed somewhere out not inside the lexical scope it still remembers the value of "a". So that what is a closure.

## Use of outer()() ?

This parenthesis is for calling a inner function. If we just execute outer() it returns inner function. So this call
will return inner function. So () will call inner function in the same line itself.
outer()() can also be written as a
var total=outer();
total();

###### Q) If we move variable declaration var a=10; to exactly the place above return inner. So that won't be a closure?

Eg)

`function outer(){`\
`function inner(){`\
`console.log(a);`\
`}`\
`var a=10;`\
`return inner;`\
`}`\
`outer()();`\
`O/P-10`

So this will still form a closure & will work the same way. This inner function will form a closure with its outer
environment & not in a particular sequence. Where it is present in the code its not like that it still forms a closure
with it.

###### What if we change var a to let a? Considering let is a block scope? What difference will it make?

Eg)

`function outer(){`\
`function inner(){`\
`console.log(a);`\
`}`\
`let a=10;`\
`return inner;`\
`}`\
`outer()();`\
`O/P-10`

So this "a" variable has a block scope here. We can't access this let a variable outside. But still it behaves the same way.
So this inner function still forms a closure even if it is let.

###### Q) But what if it has a extra parameter in outer then what will happen ? Suppose let assume that it has a parameter "b" this outer function. How will that work in them and closure?

Eg)

`function outer(b){`\
`function inner(){`\
`console.log(a,b);`\
`}`\
`let a=10;`\
`return inner;`\
`}`\
`var close=outer("helloworld")`\
`close();`\
`O/P-10 helloworld`

So it behaves the same way. Suppose we access "b" here in console.log it works the same way. Even if it is called outside
it will work the same way & it will print "a" and "b" value. So this happens because inner function forms a closure with
its outer environement & "b" is also a part of outer environement of inner function. "b" is already in same way.

###### Q) What if your outer function is nested inside the function so what will happen then. Will a inner function has access to that environement of function also? Is it the case ?

- If this outer function is nested inside one more function it will again form a closure with the environement of that
  function also.

Eg)

`function outest(){`\
`var c=20;`\
`function outer(b){`\
`function inner(){`\
`console.log(a,b,c);`\
`}`\
`let a=10;`\
`return inner;`\
`}`\
`return outer;`\
`}`

`var close=outest()("helloworld")`\
`close();`\
`O/P-10 helloworld 20`

###### Q) What if we have a global variable with the conflictedname ? So if we have a conflicting name let a?

Eg)

`function outest(){`\
`var c=20;`\
`function outer(b){`\
`function inner(){`\
`console.log(a,b,c);`\
`}`\
`let a=10;`\
`return inner;`\
`}`\
`return outer;`\
`}`\
`let a=100;`\
`var close=outest()("helloworld")`\
`close();`\
`O/P-10 helloworld 20`

- So this inner functions forms a closure with a. This inner function has access to this a & this a has a reference to (let a =10) & this variable (let a=100) is a completely new variable in global scope. So these two variables (let a=10 & let a=100) are completely different variables.It will still form a closure with this & print 10 & it does not care here.

- When the copy of inner function goes to close & it is run here. It kinds of remember a value (10). But yes if we
  didn't have (let a=10). Then it would have defaulted to the global variable because inner function forms a closure with
  its outer environment. So if it does not find a inside its outer environment. It goes up to more level deep in the
  heirarchy. So it tries to find the "a" inside the environment of outest and if it does not finds it. It goes to global
  level to resolve it over their. If it is not present in the global scope then it will result out to be an error & that
  error would be ReferenceError: a is not defined. Its not where in the scope chain so it throws an error. It tries to
  resolve in the scope chain & this inner function would not find in its scope chain. If a function was called by a
  function, which in turn was called by another function, then a chain of references to outer lexical environments
  is created. This chain is called the scope chain.

Eg)

`function x(){`\
`var i=1;`\
`setTimeout(function(){`\
`console.log(i);`\
`},1000);`\
`console.log("JavaScript");`\
`}`\
`x();`\
`O/P- JavaScript`\
`1`

- setTimeout takes the callback function & stores it in some place and attaches a timer to it. Once that timer expires it
  takes that function put its again to call stack and runs it.

###### Q1 Supppose we are given a proble to print in the console(1,2,3,4,5) after each and every second.

Eg)

`function x(){`\
`for(var i=1;i<=5;i++){`\
`setTimeout(function(){`\
`console.log(i);`\
`},i\*1000);`\
`}`\
`console.log("JavaScript");`\
`}`\
`x();`\
`O/P- JavaScript`\
`6`\
`6`\
`6`\
`6`\
`6`

- Due to closures it remembers reference to i not value of i. Even if function is taken out of its original scope, if it is executed in some other scope it still remembers it lexical environment. It can access to the variables of its lexical scope.
- When the loop runs the first time it take a copy of this function & attaches a timer to it & also remembers the reference of i. Similarly these 5 copy of these functions all of thm are pointing to same reference of i because environment for all these functions is the same reference are same. All of these copy of setTimeout callback function the reference to that i points out to same memory space.
- Second thing is JS does not wait for anything it will run the loop again and again.
- setTimeout will stores that function & move on. It does not wait for those timer to expire. When the timer expires it is too late & the value of i because the loop was constantly running this i value became 6 & when that call back function runs by that time the value of i is 6 in the memory location.

- To fix the above problem is use let instead of var.
- As let has a block scope each and every loop iteration whenever everytime loop runs i is a new copy of i alltogether & each time setTimeout is run this callback function has a new copy of i with it.
- Bcoz these let variable are block scoped so each and every time this loop is called or setTimeout function is called this function forms a closure with a new variable itself. Copy of i in each iteration is new

Eg)

`function x(){`\
`for(let i=1;i<=5;i++){`\
`setTimeout(function(){`\
`console.log(i);`\
`},i\*1000);`\
`}`\
`console.log("JavaScript");`\
`}`\
`x();`\
`O/P- JavaScript`\
`1`\
`2`\
`3`\
`4`\
`5`

###### If you cannot use let you only have to use var?

- Closure will only help you. You can do the samething. It was not working with var the copy of this i refers to the same memory location
- Somehow we have to give the new copy of i everytime & forms a closure with it.

  Eg)

  `function x(){`\
  `  for(let i=1;i<=5;i++){`\
   `function close(i){`\
  `  setTimeout(function(){`\
   `console.log(i);`\
   `},i\*1000);`\
   `}`\
   `close(i);`\
   `}`\
   `console.log("JavaScript");`\
   `}`\
   `x();`\
   `O/P- JavaScript`\
   `1`\
   `2`\
   `3`\
   `4`\
   `5`

- Using this close function we create a new copy of i. Problem was that it was referring to the same memory space. Now using this close function we created a new copy of i.

- In the following code, inner forms a closure with the lexical environment of the execution context created when foo is
  invoked, closing over variable secret:

Eg)

`function foo() {`\
`const secret = Math.trunc(Math.random() \* 100)`\
`return function inner() {`\
`console.log("The secret number is ${secret}")`\
`}`\
`}`\
`const f = foo() // "secret" is not directly accessible from outside "foo"`\
`f()`

`O/P=The secret number is 5`

- In other words: in JavaScript, functions carry a reference to a private "box of state", to which only they
  (and any other functions declared within the same lexical environment) have access. This box of the state is invisible
  to the caller of the function, delivering an excellent mechanism for data-hiding and encapsulation.

- And remember: functions in JavaScript can be passed around like variables (first-class functions), meaning these pairings of functionality and state can be passed around your program, similar to how you might pass an instance of a class around in C++.

- If JavaScript did not have closures, then more states would have to be passed between functions explicitly, making
  parameter lists longer and code noisier.

- So, if you want a function to always have access to a private piece of state, you can use a closure.

- ...and frequently we do want to associate the state with a function. For example, in Java or C++, when you add a
  private instance variable and a method to a class, you are associating the state with functionality.

- In C and most other common languages, after a function returns, all the local variables are no longer accessible because
  the stack-frame is destroyed. In JavaScript, if you declare a function within another function, then the local variables
  of the outer function can remain accessible after returning from it. In this way, in the code above, secret remains
  available to the function object inner, after it has been returned from foo.

# Uses/ Advantages of closures

1.  Module Design Patterns
2.  Function Currying
3.  Higher Order Function like onces and memoize
4.  Maintaining state in async world.
5.  SetTimeouts
6.  Iterators
7.  Data Hiding and Encapsulation

# Data Hiding-

- Suppose if we have a variable and we want to do some data privacy over it so that other function and other
  pieces of code cannot have access to that piece of data. So that is known as data hiding/privacy. We can encapsulate that data so other function and part of program cannot access it.
- But a function can also access variables defined outside the function, like this:

  Eg)

  `var count=0;`\
  `function incrementCounter(){`\
  `count++;`\
  `}`\
  `incrementCounter(); //1`\
  `incrementCounter(); //2`\
  `incrementCounter(); //3`

- In the last example, count is a global variable.In a web page, global variables belong to the page. Global variables
  can be used (and changed) by all other scripts in the page. Global variables live until the page is discarded, like
  when you navigate to another page or close the window.

- So major flaw or problem with this code is that anybody in our code can access this count variable and change it. So here comes data hiding/privacy into picture. So we want to somehow ensure that nobody else in the program can modify this
  counter & only we can do through incrementCounter function. So to acheive this we can use closures.

Eg)

`function counter(){`\
`var count=0;`\
`function incrementCounter(){`\
`count++;`\
`}`\
`}`\
`console.log(count);`\
`O/P- ReferenceError: count is not defined`

- It happens because count is a local variable. A local variable can only be used inside the function where it is defined.
- Local variables have short lives. They are created when the function is invoked, and deleted when the function is finished.

- It is hidden from other functions and other scripting code. So (var count=0) this variable is hidden so anybody from outside this scope tries to access this count. You cannot do. So it results out as an error. So we are like hiding our data or we have privacy over this count.

Eg)

`function counter(){`\
`var count=0;`\
`count += 1;`\
`return count;`\
`}`\
`counter();//1`\
`counter();//1`\
`counter();//1`

- It did not work because we reset the local counter every time we call the function.

- It can only be achieved through functions. To achieve we can return this function. A JavaScript inner function can solve this. All functions have access to the global scope. In fact, in JavaScript, all functions have access to the scope
  "above" them. JavaScript supports nested functions. Nested functions have access to the scope "above" them.

Eg)

`function counter(){`\
`var count=0;`\
`function incrementCounter(){`\
`count++;`\
`}`\
`incrementCounter()`\
`return count;`\
`}`\
`O/P=1`

- In this example, the inner function incrementCounter() has access to the count variable in the parent function.This could have solved the counter dilemma, if we could reach the incrementCounter() function from the outside. We also need to find a way to execute count = 0 only once. We need a closure.

Eg)

`const add = (function () {`\
`let count = 0;`\
`return function () {count += 1; return count}`\
`})();`

`add(); //1`\
`add(); //2`\
`add(); //3`

- The variable add is assigned to the return value of a self-invoking function.The self-invoking function only runs once.
- It sets the counter to zero (0), and returns a function expression. This way add becomes a function.
- The "wonderful" part is that it can access the count in the parent scope.This is called a JavaScript closure.
- It makes it possible for a function to have "private" variables.The counter is protected by the scope of the anonymous
  function, and can only be changed using the add function.

Eg)

`function counter(){`\
`var count=0;`\
`return function incrementCounter(){`
`count++;`\
`console.log(count);`\
`}`\
`}`\
`var counter1=counter();`\
`counter1(); 1`\
`counter1(); 2`

- When we return this function (incrementCounter) if forms a closure with counter & returns. So counter1 just call this
  counter it gives us access to this function (incrementCounter) has a closure with this variable (count). So this
  incrementCounter function will come into this counter1. So it will increment the counter.

###### So we call this counter1 function one more time. What will happen then? So like suppose call var counter2=counter(); So what will happen in thats case?

Eg)

`function counter(){`\
`var count=0;`\
`return function incrementCounter(){`\
`count++;`\
`console.log(count);`\
`}`\
`}`\
`var counter1=counter();`\
`counter1(); 1`\
`counter1(); 2`

`var counter2=counter();`\
`counter2(); 1`\
`counter2(); 2`

- Its one more advantage. So this will be a fresh counter in itself. It wont touch the scope of counter1. So this is
  completely new counter in itself and it will again form a closure. So this incrementCounter function will form a closure
  with the new counter itself. So whenever a function is run it will again form create a new independent copy itself & this
  counter variable. This incrementCounter function return will have a new counter itself. It is a separate counter altogether.

###### Is it a good way to make a counter? Is your code scalable in a sense of suppose if we have to create a DecrementCounter also? How will you go ahead and add that ? Is it good and scalable?

- Yes it is not good way if we consider that case. May be in that case we can use a Constructor function. We can have separate function for Increment and Decrement.

Eg)

`function counter(){`\
`var count=0;`\
`this.incrementCounter=function(){`\
`count++;`\
`console.log(count);`\
`}`\
`this.decrementCounter=function(){`\
`count--;`\
`console.log(count);`\
`}`\
`}`\
`var counter1= new counter(); // This is constructor function so new is used here`\
`counter1.incrementCounter(); //1`\
`counter1.incrementCounter(); // 2`\
`counter1.decrementCounter(); // 1`

- This is like a constructor function still data(count) is private & it forms a closure.

# Disadvantages of closures

- OverConsumption of memory because everytime closure is formed it consumes a lot of memory. Those closed over variables
  are not garbage collected so that it means it is accumulating lot of memory if we create a lot of closuresin our
  program because those variables are not garbage collected till the program expires.If not handled properly it leads to
  memory leakage because that memory is accumulated overtime & it can also freeze the browsers if not handled properly.

###### What is garbage collector and what it do ?

- Garbage Collector is the program in JS Engine/browser which kind of freezes up the unutilized memory. So JS is a high level programming language so in C,C++ we kind of (takes up the memory). It is upto developer how we take/have access to memory.
- How we allocate and deallocate memory? But in high level programming language JS most of the work is done by JS Engine.
- There is a garbage collector in JS so this garbage collectornwhenever there is some unsed variables it kind of take out
  of memory or freezes memory whenever there variables are no longer needed. This is what garbage collector does.

###### How are closures and garbage collected related to each other? What relation between garbage collector and closure?

Eg)

`function a(){`\
`var x=0;`\
`return function b(){`\
`console.log(x);`\
`}`\
`}`\
`a();`

- This functions "b" forms a closure with x variable. So when it is returned . When this program is run call a(). Ideally
  after executing this a(). Once this a() function execution has finished. So it should go out of memory because it is no
  longer needed. So x could have garbage collected. So this variable x is present inside a() memory. But it forms a closure b which is referencing to x. So x is being referred with any inner function & returned. This means it cannot free up "b".
  Y is a copy of "b" function & b is closed over x. x memory cannot be freezed. So if we have form more closures it kinds of accumulates a lot of memory & these variables are not garbage collected. But some of the mordern browser & V8 Engine JS engine(v8 and Chrome). They have smart garbage collection mechanism where it finds out these variables are unreachable & these smartly collects garbage variables

Eg)

`function a(){`\
`var x=0; z=10;`\
`return function b(){`\
`console.log(x);`\
`}`\
`}`\
`var y=a();`\
`y();`

# What is smartly collects garbage variables?

- Suppose z is not being used. So when this b function is returned, so z is garbage collected smartly and x is not.
- Though it forms a closure with x and z. But once it is returnedit forms a closure with x and z is returned.

# Uses of Closure

Eg)

`function outer() {`\
`let arr = [];`\
`let i;`\
`for (i = 0; i < 4; i++) {`\
// storing anonymous function
`arr[i] = function () { return i; }`\
`}`\
 `return arr;`// returning the array \
`}`

`let get_arr = outer();`

`console.log(get_arr[0]());`\
`console.log(get_arr[1]());`\
`console.log(get_arr[2]());`\
`console.log(get_arr[3]());`

- Using private variables and methods: In JavaScript, we can use private variables and methods using closures. The example below shows the use of private variables with closure.

Example: In this example, the rentPrice() function returns an object with three methods: getRent(), incRent(), and decRent(). These three methods has access to the private variable rent. However, the code outside its scope cannot directly access this variable. Hence, we can mimic object oriented programming in JavaScript.

`var rentPrice = function(initialRent) {`\
` var rent = initialRent;`\
// Define private variables for
// the closure
` return {`\
` getRent: function() {`\
` return console.log(rent);`\
`},`\
` incRent: function(amount) {`\
` rent += amount;`\
` console.log(rent);`\
` },`\
`decRent: function(amount) {`\
`rent -= amount;`\
` console.log(rent);`\
`}`\
`}`\
`}`

`var Rent = rentPrice(8000);`\

// Access the private methods
`Rent.incRent(2000);   //10000`\
`Rent.decRent(1500);   //8500`\
`Rent.decRent(1000);  //7500`\
`Rent.incRent(2000);  //9500`\
`Rent.getRent();      //9500`\

* Maintaining state between each function call: Suppose there is a function and one would like it to multiply multiple values together. This could be done with the help of a global variable as they are accessible throughout the program. However, a drawback is that they are prone to change from anywhere in the code. This can be done alternatively using closures. Closures help in maintaining the state between function calls without using a global variable.


`(function() {`\
  `var multFn = function multiply() {`\
    // This variable is local to
    // the closure and holds
    // its value inbetween
    // multiple calls
   `var mult = 9;`\
   `return function(val) {`\
    ` mult = mult * val;`\
 `    return mult;`\
`   }`\
`  };`\
  
  `var mult = multFn();`\
    
  // Call the method
  // multiple times
 ` console.log(mult(2));  //18`\
`  console.log(mult(3));  //54`\
`  console.log(mult(5)); //270`\
`}());`

* Adder function

`function makeAdder(x) {`\
`  return function (y) {`\
 `   return x + y;`\
 ` };`\
`}`\

`const add5 = makeAdder(5);`\
`const add10 = makeAdder(10);`\

`console.log(add5(2)); // 7`\
`console.log(add10(2)); // 12`\

In this example, we have defined a function makeAdder(x), that takes a single argument x, and returns a new function. The function it returns takes a single argument y, and returns the sum of x and y.

In essence, makeAdder is a function factory. It creates functions that can add a specific value to their argument. In the above example, the function factory creates two new functions—one that adds five to its argument, and one that adds 10.

add5 and add10 both form closures. They share the same function body definition, but store different lexical environments. In add5's lexical environment, x is 5, while in the lexical environment for add10, x is 10.