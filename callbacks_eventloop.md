JS is single threaded non-blocking asynchronous concurrent language,single threaded programming language or single thread Runtime. It has a single call stack and it can do one thing at a time that's what a single thread means the program can run one piece of code at a time. I have a call back, an event loop, a callback queue & some other APIs. So if we look at the Javascript Runtime itself like V8 which is the Runtime inside Chrome. Simplified view of Javascript Runtime.
The heap, where memory allocation happens, and then there's the call stack, which is where your stack frames are and all that kind of stuff.
Call Stack is basically a data structure which records basically where in the program we are, if we step into a function, we put something on to the stack, if we return from a function, we pop off top of the stack that's all stack can do
one thread === one call stack === one thing at a time

# What is callback function in JS?

To understand event loop we must understand callback functions.
Functions are first class citizens in JS. You can take a function & pass it into another function. When you do so, this function when you pass it into another function it is known as callback function.
These callback functions are very powerful in JS. It gives access to whole asynchronous world in a synchronous single threaded language.

# JS is a synchronous & single threaded language?

It can do only one thing at a time & in a specific order but due to callbacks we can do async things in JS.

Eg:
`function x(y){`\

`}`\
`x(function y(){`\

`});`\

Eg:
`function multiply(a,b){`\
`return a*b;`\
`}`\
`function square (n){`\
`return multiply (n,n);`\
`}`\
`function printSquare(n){`\
`var squared = square (n);`\
`console.log(squared);`\
`}`\
`printSquare(4);`\

* Stack 
multiply(a,b)
square (n)
printSquare(4)
main()

So if you run this run this file, there's kind of a main function right, like the file itself, so, we push that on to the stack. Then we have some function definitions, they're just like defining the state of the world, and finally we got to print square, right so print square is a function call, so we push that on to the stack, and immediately inside print square, push on to the stack, which calls multiply, now we have a return statement, we multiply A and B and we return,
when we return we pop something off the stack, so pop, multiplier of the stack, returning to square, return to print sqaure, console.log, there's no return, it's implicit, because we got to the end of the function, and we're done so that's
like the visualization of the call stack

* Stack 
multiply(a,b)-popoff
square (n) -popoff
printSquare(4) -popoff
main()-popoff

Eg:
`function foo (){`\
    `throw new Error('Oops');`\
`}`\
`function bar(){`\
`    foo();`\
`}`\
`function baz(){`\
  `  bar();`\
`}`\
`baz();`\

When you are doing browser-side-development so if we have code like this, a function baz which calls bar, which calls foo which throws error if we run it in Chrome we see this. 

`O/P- `\
`Uncaught Error: Oops !`\
`foo`\
`bar`\
`baz`\
`(anonyous function)`\

And it prints the stack trace, right, the state of the stack when that error happened, so uncaught error oops foo, bar, baz and anonyous function, which is our main. Have a function foo hich calls foo, so what's going to happen ? We have a function main 

Equally, if you've heard the term like blowing the stack, this is an example of that.

Eg:
`function foo(){`\
    `return foo();`\
`}`\
`foo();`\

Stack :
infinite loop
`foo()`\
`foo()`\
`foo()`\
`main()`\

O/P- RangeError: Maximum call stack size exceeded.

You call this function sometime else in your code. You give this responsibility of the function another function. Now it is upto x when it wants to call y.

Eg:
`setTimeout(function (){}, 5000);`\
SetTimeout takes a callback function. It is passed to setTimeout & called sometime later in your program (second argument) is time.

Eg:
`setTimeout(function (){`\
`console.log("timer");`\
`}, 5000);`\
`function x(y){`\
`console.log("x");` \
`y();`\
`}`\
`x(function y(){`\
 `console.log("y");`\
`});`\

`O/P - x`\
`y `\
`timer`\

So now lets see how this program is executed and what O/P it will give. So as JS is a synchronous single threaded language that means code will be executed one line at a time & in order. First Thing will happpen is registering a setTimeout so setTimeout will take this callback function & store in a separate space & will attach a timer to it of 5000ms & store it.
We already know JS will not wait for setTimeout to finish over here. So callback functions gives us the power of asynchronousity. It does not wait over here for 5000ms to expire and whatever needs to be done after 5000 seconds we are passing that feature or that piece of code or those lines of code as a callback function to setTimeout. JS waits for none.
SO this setTimeout asynchronous operation was not possible without these callbacks.
JS has just one call stack and we can call it as main thread. Everything whatever is exeuted inside your pag eis executed through the call stack only.

# Blocking the main thread

If any operation blocks the call stack -> Blocking the main thread
Suppose if this function x has a very heavy operation it takes around 20-30seconds to complete that function becoz JS has just one call stack, it has one main thread . It won't be able to execute any other function in the code that means everything will be blocked on in the code. That is why we say that we should never block our main thread. So we should always try to use async operations for things which take time. Just like timer code. So this setTimeout will basically takes a callbacks and execute this piece of code sometime else & it just empties or get out of the call stack.

# What happens when things are slow?
Blocking - code that is slow
console.log is not slow 
Eg: while loop from 1 to billion is slow, network requests, image requests are slow

Things which are slow and on that stack are what are blocking means.
The problem is because we are running are code in the browsers. Browsers don't give us -- well they do give us synchronous AJAX request but id we use while request which is synchronous. If we request something, i can't do anything. The browser is blocked, it's stuck , it can't do anything until those requests complete. That's because if that call stack has things on it. We've got the synchronous request, the browsers can't do anything else. It can't render, it can't run any other code,it's stuck. Not ideal, right if we want people to have nice fluid UIs, we can't block the stack.

* Solution for above problem 
The solution is asynchronous callbacks, there are no blocking functions in the browser, equally in the node, they're all made asynchronous, which basically means we run some code, give it a callback and run that later, if you've seen JS you've have seen asynchronous callbacks, what does this actually look like. 

# Power of callbacks

So if JS didn't have this first class functions & we didn't have this callback function & we could not have passed these functions to another functions. We couldn't have been able todo this asynchronous operations. Using this WEBAPIS & this setTimeout & this callback function we can't achieve this asynchronous operations.

# Deep Dive into Event Listeners

Eg
`<button id="clickMe">Click Me</button>`\
`document.getElementById("clickMe").addEventListener("click", functon xyz(){`\
 `console.log("Button Clicked");`\  
`})`\

xyz() will come inside the callback

# Closures Demo alonG the Event Listeners

O) Suppose if we have to count how many times the button is clicked& if we have to print that.

- Global Variable
  Eg:
  ` let count=0;`\
  ` document.getElementById("clcikMe).addEventListener("Click",function xyz(){`\
  `     console.log("Button clciked", ++count);`\
  ` } )`\
  `O/P:`
  `Button clicked 1`\
  `Button clicked 2`\
  `Button clicked 3`\
  `Button clicked 4`\

Using a global Variable sis not a good solution.
Eg:
`function addEventListeners(){`\
`    let count=0;`\
 `document.getElementById("clickMe").addEventListener("click", function xyz(){`\
 `console.log("Button Clicked", ++count);`\  
 ` })`\
`}`\
`addEventListener();`\

Callback function xyz() is formimg a closure with it outside scope. So this xyz() is forming a closure with it outside scope. So this xyz() has access to its closures.
Handler is a callaback function:--> f xyz() ;
Scope is lexical scope that the function carries. in case of browser its window
`Global {window}`\
`Closure (addEventListener){`\
 `count :0;`\
`}`\

# Garbage Collection & remove Event Listeners

EventListeners are heavy (they take memory). It forms a closure. Call Stack is empty. Not executed any of the code. This program can not free up the memory. It can not free up the count because you never know when somebody on the page can click on the button & we need this closure this. So we cannot free up the extra memory. That is why EventListeners are heavy & this is why we removeEventListeners when we are not using them.
Suppose our code has thousand of buttons or Event Listeners attached on click, on mouse hover, on scroll so our page can go a lot of slow because of these so many closures like sitting in ur memory & consuming a lot of memory of all their scopes & all these callback function will hold on those scopes. Good Practice is to remove this event listener to free up.
All these variables which wee held by closures will be garbage collected.

# Asynchronous JS and Event Loop
JS is a synchronous single threaded language. It has one call stack and it can do only one thing at a time. This callStack is present inside the JS Engine and all the code of JS is executed inside the call stack.

Eg:
`function a(){`\
 `console.log("a is called");`\
`}`\
`a();`\
`console.log("End");`\
`O/P- a is called `\
`End`\

First of all when any JS program is run a global execution context is created. Global execution context is created & pushed inside call stack. In Global execution context this whole code will run line by line. Function definition of 'a'. So a will allocate memory and this function will be stored. a() function invocation.
In case of functon invocation a execution context is created for function 'a' to execute this function & execution context is again pushed inside call stack. It sees that there is nothing more to be executed inside 'a'. So it pops out this a() execution context out of the call stack. a() execution context is deleted. Global execution context pops out of the call stack.

But what if we need to wait for something. But what if we have a program or script that needs to wait for 5000ms.
No we cannot do that becoz what ever comes inside call stack is quickly executed inside call stack. Call stack does not have timer.

Browser is most remarakable thing in history of mankind.We need to have access to local storage, timer, bluetooth, geo-location and what ever things are present in browser.

We need to have access to these superpowers Web APIs which are present inside browser including JS Engine. Web APIs are extra things which browser provides.
Web APIs:
setTimeout()
DOM APIs- document.get
fetch() make connection to external servers
Local Storage
console
Location

These are not a part of JS but superpowers of browsers.
Browser gives us access to inside JS Engine , Call Stack to use all these superpowers using window.
We will get inside Call Stack because of this Global Object (window). In browser it is window.
window.setTimeout() === setTimeout()
window.localStorage() === localStorage()
window.console.log() === console.log()

Because window is Global Object and setTimeout present inside window object or at the global scope.

Eg:
`console.log("start");`\
`setTimeout(function cb(){`\
` console.log("callback");`\
`}, 5000);`\
`console.log("end");`\

- console
  `start`\
  `end`\
  `callback`\

- Web APIs
  `cb() timer for 5000ms start`\

Global execution context is pops out of the call stack but this timer is still running. As soon as timer expires this callback (cb) function needs to be executed.
And we know all the code in JS is executed inside Call Stack. So we somehow need this cb() function inside Call Stack. But it cannot directly go inside call stack.

* JS engine 
cb()

* WEB APIs
cb() deleted
call stack

* event-loop    

* callback queues (cb)
 deleted

It will go to the Call Stack through the callback queue.

Job of Event Loop is to check this callabck queue has some function and puts this functions of this callabck queue into the call stack. Acts like a gate keeper and checks whether we have something in the callback queue & if we have something (just like callback method (cb)) it just pushes it inside Call Stack. It quickly executes this callback function. It creates a execution context pushes it into Call Stack. Its job is to look at the stack and look at the task queue. If the stack is empty it takes the first thing on the queue and pushes it on to the stack which effectively run it 

That's true that JS Runtime can only do one thing at one time. It can't make AJAX request while you're doing other code. It can't do a setTimeout while you're doing another code. The reason we can do things concurrently is that the browser is more than just the Runtime. 

Eg:
`console.log("Hi");`\
`setTimeout(function cb(){`\
` console.log("there");`\
`}, 0);`\
`console.log("JSconfEU");`\

setTimeout zero. The reason is, generally, if you're trying to defer something until the stack is clear. So we know looking at this, if you've written JS, that we're going to see the same result, we're going to see "Hi", "JSconfEU","there" is going to appear at the end. The setTimeouT zero, now t's going to complete immediately and push it on the queue, remember what i said about event loop, it has to wait till the stack is clear before it can push the callback into the stack, so your stack is going to continue to run console.log "Hi", "JSconfEU" and clear, now the vent loop can kick in 
and call your callback. That's like an example of setTimeout zero, is deferring that execution of code to zero for whatever reason to the end of the stack or until the stack is clear.

*stack
main()
setTimeout(cb);
JSconfEU

* console 
Hi
JSconfEU
there 

* Web APIs
  cb() (deleted)

* event-loop 

* callback queues (cb)
  deleted

That's like an example of setTimeout zero, is deferring that execution of code, for, whatever reason to the end of the stack or until the stack is clear.

Eg:
`console.log("start");`\
`document.getElementById("btn").addEventListener("click",function cb(){`\
 `console.log("callback");`\
`});`\
`console.log("end");`\

* console 
  start
  end
  callback 

* Web APIs
  cb(), (deleted)
 click 

* event-loop 

* callback queues (cb)
  deleted

In Web APIs environment whenever you see this addEventListener it basically registering this callback method & attach an event to it. Global execution context is popped out of the call stack. Global execution context is gone. This event listener will stay in Web APIs environment until & unless we explicitly remove that eventlistener or close thr browser.
When user click on the btn the cb function is moved to the callback queue & sits inside and wait over here for its turn to get executed.

Event Loop has one job. Its only job is to like continuously monitor the call stack & callback queue. If call stack is empty & event loop sees that there is also a function waitng to be executed inside callback queue. it takes this function & pushes inside call stack & callback method is quickly executed. It vanishes from callback queue also.

- Why do we need this callback queue?
  Suppose a user clicks on the btn 7-8 times continuously. cb functon will be pushed inside callback queue(7-8 times) & eventloop will continuously check if Call Stack is empty or not. If this is empty, it will push callback function one by one & executes callback queue containing all the lined up callback functions.

cb cb cb cb cb cb cb cb cb cb cb cb
callback queue

There are lot of eventListener & lot of things happening inside the browser (a lot of timers, eventListener). So that is why we need to queue all of these callback functions together so they get a chance one after the other. JS has just one Call Stack & everything is executed here only.

- How fetch() works ?
  It behaves differently from setTimeout & eventListener.

Eg:
`console.log("start");`\
`setTimeout(function cbT(){`\
`    console.log("CB SetTimeout");`\
`}, 5000);`\
`fetch("https://api.netflix.com").then(function cbF(){`\
`console.log("CB Netflix"); ` \
`});`\

---

---

`//many lines of code `
`console.log("end");`

- console _ Web APIs
  start cbT(), cbF()
  end
  CB Netflix  
  Cb SetTimeout  
   _ event-loop _ microtask queues (cbF)
  _ callback queue (cbT)

Fetch() to make network call. Fetch basically goes & request a API call. Fetch returns a promise & we have to call a callback function which will be executed once this Promise is resolved.
This cbT() function s waiting for this timer to expire.
cbF() is waitingb for data to be returned from the servers. Once we get data over here and cbF() will now be ready to be executed.
Suppose data we get in some from servers. Now the cbF() function is ready to be executed. cbF() wll go to the callback queue ??
That what we aspect ??
But No. That won't happen.

# Microtask queue

Similar to callback queue we have a microtask queue but microtask queue has higher priority. Whatever functions will come inside this queue will be executed first and functions inside callback queue are executed later.

In case of Promises in case of network calls it will go to microtask queue.

Job of event loop is to keep on checking whether Call Stack is empty or not. Once the callstack is empty it gives the chance to all the functions of queue inside the call stack.

We are not yet done executing this code. End line was still left but we already got response from the servers. But we are still executing (End) code. Suppose there are 1000 lines of line above (End) code. Millions of line of code & it takes sometime to execute this. We already have cbF function waiting inside MicroTask queue to be executed. But we are running millions lines of code in our main thread. Lets see (cbF) when gets the chance. Meanwhile we are running millions lines of code the timer also expires . Browser is not doing just one thing its only JS Engine that is doing one thing at a time. But the browser is doing a lot of things behind the scenes.

Now cbT is ready to be executed. It will go instead callback queue.

After printing console(End). Global Execution Context will be popped out of the stack. Event Loop is continuously monitoring call stack. Once call stack is empty. It also sees some task pending inside microta8sk queue & callback queue.
Because microtask queue has higher priority than callback queue so this gives a chance to this cbF first to get inside call Stack. Now cbF() is popped out from call stack & from microtask queue also.

Event Loop again monitoring cbT will go inside call Stack & whole code is executed.

# Microtask Queue is given higher priority ? What can come inside microtask queue ?

All the callback function which come through promises will go inside Microstask queue. Promises and mutation observer goes inside microstask queue.

# Mutation Observer

Keep on checking if there is something mutation in the DOM tree or not. If there is some mutation in the DOM tree it can execute the callback function.

CallBack Queue- setTimeout(),DOM APIs, goes inside callback queue,Also known as Task Queue.
If there are three task pending inside the microtask queueb& one inside callback queue. So this eventloop will only gives this callback queue a chance once and all tasks from microtask queue are completed.

Event Loop gives microtask queue first chance. Suppose a mcrotask creates a new microtask in itself & this creates another microtask & it goes on. SO this task in callback queue wllnever get a change to execute . "Starvation of The Task Inside CallBack Queue.

