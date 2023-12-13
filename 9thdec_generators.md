# Generators

Regular functions return only one, single value (or nothing).
Generators can return (“yield”) multiple values, one after another, on-demand. They work great with iterables, allowing to create data streams with ease.

## Generator functions

Eg:
`function\* generateSequence() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

To create a generator, we need a special syntax construct: function\*, so-called “generator function”.

- purpose of this is to tell JS that it is a generator function & then inside generator function we have three separate yield statements yield 1, 2, and 3. Yield is essentially a special type of return keyword that is only useful inside a generator.

The purpose of a generator is essentially run some code and then return a value and then run some more code and return another value & on until you get to the end of all of the code inside of a generator. So in order to run a generator we need to run the generator function which is going to return us a generator object that allows us to manipulate and use this generator

Eg:
`function\* generateSequence() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

`let generatorObject = generateSequence();`\
`alert(generatorObject); // [object Generator]`\

Generator functions behave differently from regular ones. When such function is called, it doesn’t run its code. Instead it returns a special object, called “generator object”, to manage the execution.
The function code execution hasn’t started yet:
![user reference](images/generateSequence-1.svg "reference")

The main method of a generator is next(). When called, it runs the execution until the nearest yield <value> statement (value can be omitted, then it’s undefined). Then the function execution pauses, and the yielded value is returned to the outer code.
The result of next() is always an object with two properties:
value: the yielded value.
done: true if the function code has finished, otherwise false.
false means that there's still more code to run and true meaning that there's nothing and the value is going to be whatever yielded

For instance, here we create the generator and get its first yielded value:

Eg:

`function\* generateSequence() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

`let generator = generateSequence();`\

`let one = generator.next();`\

`alert(JSON.stringify(one)); // {value: 1, done: false}`\

As of now, we got the first value only, and the function execution is on the second line:

![user reference](images/generateSequence-2.svg "reference")
Let’s call generator.next() again. It resumes the code execution and returns the next yield:

`let two = generator.next();`\

`alert(JSON.stringify(two)); // {value: 2, done: false}`\

![user reference](images/generateSequence-3.svg "reference")

And, if we call it a third time, the execution reaches the return statement that finishes the function:

`let three = generator.next();`\

`alert(JSON.stringify(three)); // {value: 3, done: true}`\

![user reference](images/generateSequence-4.svg "reference")

Now the generator is done. We should see it from done:true and process value:3 as the final result.

New calls to generator.next() don’t make sense any more. If we do them, they return the same object: {done: true}.

Eg :

`function\* simpleGenerator() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

`let generatorObject =simpleGenerator();`\

`const obj=generatorObject.next();`\
`console.log(obj);// {value: 1, done: false}`\
`console.log(generatorObject.next());// {value: 2, done: false}`\
`console.log(generatorObject.next());// {value: 3, done: false}`\
`console.log(generatorObject.next());// {value: undefined, done: true}`\

Eg:

`function\* simpleGenerator() {`\
`console.log("Before 1")`\
`yield 1;`\
`console.log("After 1")`\
`console.log("Before 2")`\
`yield 2;`\
`console.log("After 2")`\
`console.log("Before 3")`\
`return 3;`\
`console.log("After 3")`\
`}`\

`let generatorObject =simpleGenerator();`\

Nothing is printed out

Eg:

`function\* simpleGenerator() {`\
`console.log("Before 1")`\
`yield 1;`\
`console.log("After 1")`\
`console.log("Before 2")`\
`yield 2;`\
`console.log("After 2")`\
`console.log("Before 3")`\
`return 3;`\
`console.log("After 3")`\
`}`\

`let generatorObject =simpleGenerator();`\
`const obj=generatorObject.next();`\
`console.log(obj);`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\

O/P-
Before 1
{value: 1, done: false}
After 1
Before 2
{value: 2, done: false}
After 2
Before 3
{value: 3, done: true}
{value: undefined, done: true}

The Generator object is returned by a generator function and it conforms to both the iterable protocol and the iterator protocol.
Generator is a subclass of the hidden Iterator class.

Eg:

`const foo = function\* () {`\
`yield 'a';`\
`yield 'b';`\
`yield 'c';`\
`};`\

`let str = '';`\
`for (const val of foo()) {`\
`str = str + val;`\
`}`\

`console.log(str);//abc`\

Eg:

`function* getPage(list, pageSize = 1) {`\
 ` for (let index = 0; index < list.length; index += pageSize) {`\
   ` yield list.slice(index, index + pageSize);`\
  `}`\
`}`\

`const list = [1, 2, 3, 4, 5, 6, 7, 8];`\
`const page = getPage(list, 3); // Generator { }`\

`page.next(); // { value: [1, 2, 3], done: false }`\
`page.next(); // { value: [4, 5, 6], done: false }`\
`page.next(); // { value: [7, 8], done: false }`\
`page.next(); // { value: undefined, done: true }`\

## Constructor

The Generator constructor is not available globally. Instances of Generator must be returned from generator functions:

Eg:

`function\* generator() {`\
`yield 1;`\
`yield 2;`\
`yield 3;`\
`}`\

`const gen = generator(); // "Generator { }"`\

`console.log(gen.next().value); // 1`\
`console.log(gen.next().value); // 2`\
`console.log(gen.next().value); // 3`\

In fact, there's no JavaScript entity that corresponds to the Generator constructor. There's only a hidden object which is the prototype object shared by all objects created by generator functions. This object is often stylized as Generator.prototype to make it look like a class, but it should be more appropriately called GeneratorFunction.prototype.prototype, because GeneratorFunction is an actual JavaScript entity.

## Instance properties

These properties are defined on Generator.prototype and shared by all Generator instances.

Generator.prototype.constructor- The constructor function that created the instance object. For Generator instances, the initial value is GeneratorFunction.prototype.

Note: Generator objects do not store a reference to the generator function that created them.

Generator.prototype[@@toStringTag]- The initial value of the @@toStringTag property is the string "Generator". This property is used in Object.prototype.toString().

## Instance methods

Also inherits instance methods from its parent Iterator.
//Generator.prototype.next()
Returns a value yielded by the yield expression.

//Generator.prototype.return()
Acts as if a return statement is inserted in the generator's body at the current suspended position, which finishes the generator and allows the generator to perform any cleanup tasks when combined with a try...finally block.

//Generator.prototype.throw()
Acts as if a throw statement is inserted in the generator's body at the current suspended position, which informs the generator of an error condition and allows it to handle the error, or perform cleanup and close itself.

## Multiple Generators

Eg:

`function\* simpleGenerator() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

`let generatorObject =simpleGenerator();`\
`let generatorObject2 =simpleGenerator();`\
`const obj=generatorObject.next();`\
`console.log(generatorObject.next());`\
`console.log(generatorObject2.next());`\
`console.log(generatorObject2.next());`\

O/P-
{value: 1, done: false}
{value: 2, done: false}
{value: 1, done: false}
{value: 2, done: false}

Eg:

`function\* simpleGenerator() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

`let generatorObject =simpleGenerator();`\
`const obj=generatorObject.next();`\
`console.log(generatorObject.next());`\
`let generatorObject2 =simpleGenerator();`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\

O/P-
{value: 1, done: false}
{value: 2, done: false}
{value: 3, done: false}
{value: undefined, done: true}

## UseCases / Applications

### Generators are iterable

generators are iterable.

We can loop over their values using for..of:

Eg:
`function\* generateSequence() {`\
`yield 1;`\
`yield 2;`\
`return 3;`\
`}`\

`let generator = generateSequence();`\

`for(let value of generator) {`\
`alert(value); // 1, then 2`\
`}`\

Looks a lot nicer than calling .next().value, right?

…But please note: the example above shows 1, then 2, and that’s all. It doesn’t show 3!

It’s because for..of iteration ignores the last value, when done: true. So, if we want all results to be shown by for..of, we must return them with yield:

Eg:

`function\* generateSequence() {`\
`yield 1;`\
`yield 2;`\
`yield 3;`\
`}`\

`let generator = generateSequence();`\

`for(let value of generator) {`\
`alert(value); // 1, then 2, then 3`\
`}`\

As generators are iterable, we can call all related functionality, e.g. the spread syntax ...:

Eg:

`function\* generateSequence() {`\
`yield 1;`\
`yield 2;`\
`yield 3;`\
`}`\

`let sequence = [0, ...generateSequence()];`\

`alert(sequence); // 0, 1, 2, 3`\
In the code above, ...generateSequence() turns the iterable generator object into an array of items

## Using generators for iterables

Here, let’s remember the code:

Eg:
`let range = {`\
`from: 1,`\
`to: 5,`\

// for..of range calls this method once in the very beginning
[Symbol.iterator]() {
// ...it returns the iterator object:
// onward, for..of works only with that object, asking it for next values
`return {`\
`current: this.from,`\
`last: this.to,`\

      // next() is called on each iteration by the for..of loop
 `     next() {`\
        // it should return the value as an object {done:.., value :...}
      `  if (this.current <= this.last) {`\
          `return { done: false, value: this.current++ };`\
  `      } else {`\
     `     return { done: true };`\
  `      }`\
 `     }`\
 `   };`\

`}`\
`};`\

// iteration over range returns numbers from range.from to range.to
`alert([...range]); // 1,2,3,4,5`\

We can use a generator function for iteration by providing it as Symbol.iterator.

Here’s the same range, but much more compact:

`let range = {`\
`from: 1,`\
`to: 5,`\

_[Symbol.iterator]() { // a shorthand for [Symbol.iterator]: function_()
`for(let value = this.from; value <= this.to; value++) {`\
`yield value;`\
`}`\
`}`\
`};`\

`alert( [...range] ); // 1,2,3,4,5`\

That works, because range[Symbol.iterator]() now returns a generator, and generator methods are exactly what for..of expects:

it has a .next() method
that returns values in the form {value: ..., done: true/false}

### Infinite Loop

In JS you can't do an infinite loop because you'll just loop forever and freeze up your program but with generators we can create an infinite loop that actually doesn't stop your computer and freeze your program because you're only executing it one step at a time

#### Generate Ids
Eg:
`function* generateId(){`\
`let id =1;`\
`while(true){`\
`yield id`\
`id++`\
`}`\
`}`\
`const generatorObject = generateId();`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\

O/P-
{value: 1, done: false}
{value: 2, done: false}
{value: 3, done: false}
{value: 4, done: false}
{value: 5, done: false}
{value: 6, done: false}
{value: 7, done: false}
{value: 8, done: false}
{value: 9, done: false}

### Iterator

A function or object such as our generator object that has a next property that we can call on it & the next property is going to give us a value & it's going to give us a done flag to let us know if we're done iterating or if we can continue to iterate

Eg:
`function* generator(array){`\
`for(let i=0;i<array.length;i++){`\
`yield array[i];`\
`}`\
`}`\
`const generatorObject=generator([1,3,5]);`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\

O/P-
{value: 1, done: false}
{value: 3, done: false}
{value: 5, done: false}
{value: undefined, done:true}

Eg:
`function* generateId(){`\
`let id =1;`\
`while(true){`\
`const increment= yield id;`\
`if(increment != null){`\
`id+=increment;`\
`}else{`\
`id++`\
`}`\
`}`\
`}`\

`const generatorObject = generateId();`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next(4));`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next(4));`\
`console.log(generatorObject.next(3));`\
`console.log(generatorObject.next());`\

O/P-
{value: 1, done: false}
{value: 5, done: false}
{value: 6, done: false}
{value: 7, done: false}
{value: 1, done: false}
{value: 5, done: false}
{value: 8, done: false}
{value: 9, done: false}

Has a function called return & return allows you to essentially exit out of a generator no matter how much more you have to go it'll return whatever value you pass it.
Eg:
`function* generateId(){`\
`let id =1;`\
`while(true){`\
`const increment= yield id;`\
`if(increment != null){`\
`id+=increment;`\
`}else{`\
`id++`\
`}`\
`}`\
`}`\
`const generatorObject = generateId();`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.next());`\
`console.log(generatorObject.return(10));`\
`console.log(generatorObject.next());`\

O/P-
{value: 1, done: false}
{value: 2, done: false}
{value: 10, done: true}
{value: undefined, done: true}

Call return it's exiting out of our generator function as if it finished & it's just going to return this value right here so it immediately exits our generator & does not allow us to have any more information in our generator at all. This is really great if you need to exit out of the generator prematurely you just use return & it's going to exit out immediately.

## Generator composition

Generator composition is a special feature of generators that allows to transparently “embed” generators in each other.
For instance, we have a function that generates a sequence of numbers:
Eg:
`function* generateSequence(start, end) {`\
`for (let i = start; i <= end; i++)yield i;`\
`}`\
Now we’d like to reuse it to generate a more complex sequence:

first, digits 0..9 (with character codes 48…57),
followed by uppercase alphabet letters A..Z (character codes 65…90)
followed by lowercase alphabet letters a..z (character codes 97…122)

We can use this sequence e.g. to create passwords by selecting characters from it (could add syntax characters as well), but let’s generate it first.

In a regular function, to combine results from multiple other functions, we call them, store the results, and then join at the end.

For generators, there’s a special yield\* syntax to “embed” (compose) one generator into another.

The composed generator:
Eg:
`function* generateSequence(start, end) {`\
`for (let i = start; i <= end; i++) yield i;`\
`}`\

`function* generatePasswordCodes() {`\
// 0..9
`yield* generateSequence(48, 57);`\
// A..Z
`yield* generateSequence(65, 90);`\
// a..z
`yield* generateSequence(97, 122);`\
`}`\

`let str = '';`\

`for(let code of generatePasswordCodes()) {`\
`str += String.fromCharCode(code);`\
`}`\

`alert(str); // 0..9A..Za..z`\

The yield* directive delegates the execution to another generator. This term means that yield* gen iterates over the generator gen and transparently forwards its yields outside. As if the values were yielded by the outer generator.

The result is the same as if we inlined the code from nested generators:
Eg:
`function* generateSequence(start, end) {`\
`for (let i = start; i <= end; i++) yield i;`\
`}`\

`function* generateAlphaNum() {`\
// yield\* generateSequence(48, 57);
`for (let i = 48; i <= 57; i++) yield i;`\
// yield\* generateSequence(65, 90);
`for (let i = 65; i <= 90; i++) yield i;`\
// yield\* generateSequence(97, 122);
`for (let i = 97; i <= 122; i++) yield i;`\
`}`\

`let str = '';`\

`for(let code of generateAlphaNum()) {`\
`str += String.fromCharCode(code);`\
`}`\

`alert(str); // 0..9A..Za..z`\

A generator composition is a natural way to insert a flow of one generator into another. It doesn’t use extra memory to store intermediate results.

## “yield” is a two-way street

yield is a two-way street: it not only returns the result to the outside, but also can pass the value inside the generator.
To do so, we should call generator.next(arg), with an argument. That argument becomes the result of yield.
Eg:

`function* gen() {`\
// Pass a question to the outer code and wait for an answer
`let result = yield "2 + 2 = ?"; // (*)`\
`alert(result);`\
`}`\
`let generator = gen();`\
`let question = generator.next().value; // <-- yield returns the value`\
`generator.next(4); // --> pass the result into the generator`\

![user reference](images/genYield2.svg "reference")

The first call generator.next() should be always made without an argument (the argument is ignored if passed). It starts the execution and returns the result of the first yield "2+2=?". At this point the generator pauses the execution, while staying on the line (\*).
Then, as shown at the picture above, the result of yield gets into the question variable in the calling code.
On generator.next(4), the generator resumes, and 4 gets in as the result: let result = 4.

Please note, the outer code does not have to immediately call next(4). It may take time. That’s not a problem: the generator will wait.

For instance:
// resume the generator after some time
setTimeout(() => generator.next(4), 1000);

As we can see, unlike regular functions, a generator and the calling code can exchange results by passing values in next/yield.

To make things more obvious, here’s another example, with more calls:
Eg:
`function* gen() {`\
`let ask1 = yield "2 + 2 = ?";`\
`alert(ask1); // 4`\
`let ask2 = yield "3 \* 3 = ?"`\
`alert(ask2); // 9`\
`}`\

`let generator = gen();`\

`alert( generator.next().value ); // "2 + 2 = ?"`\

`alert( generator.next(4).value ); // "3 \* 3 = ?"`\

`alert( generator.next(9).done ); // true`\

The execution picture:
![user reference](images/genYield2-2.svg "reference")

The first .next() starts the execution… It reaches the first yield.
The result is returned to the outer code.
The second .next(4) passes 4 back to the generator as the result of the first yield, and resumes the execution.
…It reaches the second yield, that becomes the result of the generator call.
The third next(9) passes 9 into the generator as the result of the second yield and resumes the execution that reaches the end of the function, so done: true.

Each next(value) (excluding the first one) passes a value into the generator, that becomes the result of the current yield, and then gets back the result of the next yield.

## generator.throw

To pass an error into a yield, we should call generator.throw(err). In that case, the err is thrown in the line with that yield.
For instance, here the yield of "2 + 2 = ?" leads to an error:

Eg:
`function* gen() {`\
`try {`\
`let result = yield "2 + 2 = ?"; // (1)`\
 `alert("The execution does not reach here, because the exception is thrown above");`\
`} catch(e) {`\
`alert(e); // shows the error`\
`}`\
`}`\

`let generator = gen();`\

`let question = generator.next().value;`\

`generator.throw(new Error("The answer is not found in my database")); // (2)`\

O/P:
The answer is not found in my database.
{value:undefined, done:true}

The error, thrown into the generator at line (2) leads to an exception in line (1) with yield. In the example above, try..catch catches it and shows it.
If we don’t catch it, then just like any exception, it “falls out” the generator into the calling code.
The current line of the calling code is the line with generator.throw, labelled as (2). So we can catch it here, like this:

Eg:
`function* generate() {`\
`let result = yield "2 + 2 = ?"; // Error in this line`\
`}`\

`let generator = generate();`\

`let question = generator.next().value;`\

`try {`\
`generator.throw(new Error("The answer is not found in my database"));`\
`} catch(e) {`\
`alert(e); // shows the error`\
`}`\

O/P:
The answer is not found in my database.
{value:undefined, done:true}

If we don’t catch the error there, then, as usual, it falls through to the outer calling code (if any) and, if uncaught, kills the script.

It is just going to allow you to throw an error if you have an error we can just say you know

console.log(generateObject.throw(new Error ("Hi")));

If we save you can see it's throwing that error high and it's throwing it for our generator this isn't really something super useful unless you're kind of like writing a library behind the scenes but it's important to know that it's there and that.

Eg:
`function* gen() {`\
`  while (true) {`\
   ` try {`\
     `yield 42;`\
    `} catch (e) {`\
     ` console.log("Error caught!");`\
   ` }`\
  `}`\
`}`\

`const g = gen();`\
`g.next();`\
// { value: 42, done: false }
`g.throw(new Error("Something went wrong"));`\
// "Error caught!"
// { value: 42, done: false }

## generator.return

generator.return(value) finishes the generator execution and return the given value.

Eg:
`function* gen() {`\
`yield 1;`\
`yield 2;`\
`yield 3;`\
`}`\

`const g = gen();`\

`g.next(); // { value: 1, done: false }`\
`g.return('foo'); // { value: "foo", done: true }`\
`g.next(); // { value: undefined, done: true }`\

Eg:

`function* gen() {`\
  `yield 1;`\
  `yield 2;`\
  `yield 3;`\
`}`\

`const g = gen();`\
`g.next(); // { value: 1, done: false }`\
`g.next(); // { value: 2, done: false }`\
`g.next(); // { value: 3, done: false }`\
`g.next(); // { value: undefined, done: true }`\
`g.return(); // { value: undefined, done: true }`\
`g.return(1); // { value: 1, done: true }`\

Eg:

`function* gen() {`\
 ` yield 1;`\
 ` try {`\
  `  yield 2;`\
   ` yield 3;`\
  `} finally {`\
    `yield "cleanup";`\
  `}`\
`}`\

`const g1 = gen();`\
`g1.next(); // { value: 1, done: false }`\

// Execution is suspended before the try...finally.
`g1.return("early return"); // { value: 'early return', done: true }`\

`const g2 = gen();`\
`g2.next(); // { value: 1, done: false }`\
`g2.next(); // { value: 2, done: false }`\

// Execution is suspended within the try...finally.
`g2.return("early return"); // { value: 'cleanup', done: false }`\

// The completion value is preserved
`g2.next(); // { value: 'early return', done: true }`\

// Generator is in the completed state
`g2.return("not so early return"); // { value: 'not so early return', done: true }`\

If we again use generator.return() in a completed generator, it will return that value again

Often we don’t use it, as most of time we want to get all returning values, but it can be useful when we want to stop generator in a specific condition.

## Summary

Generators are created by generator functions function\* f(…) {…}.
Inside generators (only) there exists a yield operator.
The outer code and the generator may exchange results via next/yield calls.

In modern JavaScript, generators are rarely used. But sometimes they come in handy, because the ability of a function to exchange data with the calling code during the execution is quite unique. And, surely, they are great for making iterable objects.

## Tasks

Pseudo-random generator
There are many areas where we need random data.

One of them is testing. We may need random data: text, numbers, etc. to test things out well.

In JavaScript, we could use Math.random(). But if something goes wrong, we’d like to be able to repeat the test, using exactly the same data.

For that, so called “seeded pseudo-random generators” are used. They take a “seed”, the first value, and then generate the next ones using a formula so that the same seed yields the same sequence, and hence the whole flow is easily reproducible. We only need to remember the seed to repeat it.

An example of such formula, that generates somewhat uniformly distributed values:
next = previous \* 16807 % 2147483647

1. 16807
2. 282475249
3. 1622650073

The task is to create a generator function pseudoRandom(seed) that takes seed and creates the generator with this formula.
Usage example:

let generator = pseudoRandom(1);

alert(generator.next().value); // 16807
alert(generator.next().value); // 282475249
alert(generator.next().value); // 1622650073

O/P=

function* pseudoRandom(seed) {
let value = seed;

while(true) {
value = value * 16807 % 2147483647;
yield value;
}

};

let generator = pseudoRandom(1);

alert(generator.next().value); // 16807
alert(generator.next().value); // 282475249
alert(generator.next().value); // 1622650073

Please note, the same can be done with a regular function, like this:

function pseudoRandom(seed) {
let value = seed;

return function() {
value = value * 16807 % 2147483647;
return value;
}
}

let generator = pseudoRandom(1);

alert(generator()); // 16807
alert(generator()); // 282475249
alert(generator()); // 1622650073

That also works. But then we lose ability to iterate with for..of and to use generator composition, that may be useful elsewhere.
