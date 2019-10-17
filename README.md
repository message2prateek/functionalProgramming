# Functional Programming

Functional programming is a programming paradigm, meaning that it is a way of thinking about software construction based on some fundamental, defining principles. Other examples of programming paradigms include object oriented programming(e.g. Java) and procedural programming(e.g. COBOL).

## Why Functional?
Functional code tends to be more concise, more predictable, and easier to test than imperative or object oriented code — but if you’re unfamiliar with it and the common patterns associated with it, functional code can also seem a lot more dense.

## Tell me more about Functional Programming

Functional programming revolves around the idea that a program is made of a set of functions and they follow certain rules. There are no classes, there is no inheritance and the patterns that you will encounter are a lot different here.

Functional programming (often abbreviated FP) is the process of building software by 
- composing _pure functions_ and,
-  avoiding _shared state_, _mutable data_, and _side-effects_. 


Let's learn more about these concepts

### 1. Pure functions

A pure function is a function which:

1. Given the same inputs, always returns the same output, and
1. Has no side-effects

```js
// impure function
const getRandomNumber = () => Math.random().toFixed(2)
getRandomNumber()  // .52
getRandomNumber()  // .90 
//this function yeilds different result based when called multiple times, hence it's not a pure function.
```

```js
// pure function
const addNumbers = (x, y) => x +y
add(2, 3)  // 5
add(2, 3)  // 5

//calling this function yeilds the same result, even when called multiple times
```

We can replace a `pure function` call with its resulting value without changing the meaning of the program i.e. a pure function is `Refrentially Transparent`.


```js
//pure function
const addPure = (x, y) => x + y
console.log(addPure(2, 3)) //5
console.log(addPure(2,3)) //5

//impure function - Shared state
const x = 2, y=3  //shared state
const addImpure = () => x + y
console.log(addImpure()) // 5
x = 6
console.log(addImpure()) // 9 . The value returned by the function depends on the current value of x.
```


## 2.1 Avoid side effects
A side effect is any application state change that is observable outside the called function other than its return value. Side effects include:

- Modifying `Shared State` - any external variable or object property (e.g., a global variable, or a variable in the parent function scope chain)
- Logging to the console
- Writing to the screen
- Writing to a file
- Writing to the network
- Triggering any external process
- Calling any other functions with side-effects

```js
// impure function - side effect
const displayName = name => console.log(`Name is `${name}`)
displayName('Prateek') 
```

Side effects are mostly avoided in functional programming, which makes the effects of a program much easier to understand, and much easier to test.

## 2.2 Avoid shared state

Shared state is any variable, object, or memory space that exists in a shared scope, or as the property of an object being passed between scopes. A shared scope can include global scope or closure scopes. Often, in object oriented programming, objects are shared between scopes by adding properties to other objects.

For example, a computer game might have a master game object, with characters and game items stored as properties owned by that object. Functional programming avoids shared state — instead relying on immutable data structures and pure calculations to derive new data from existing data. 

The problem with shared state is that in order to understand the effects of a function, you have to know the entire history of every shared variable that the function uses or affects. Race condition  is a very common bug associated with shared state.

Another common problem associated with shared state is that changing the order in which functions are called can cause a cascade of failures because functions which act on shared state are timing dependent:

```js
// With shared state, the order in which function calls are made
// changes the result of the function calls.
const x = {
  val: 2
};

const x1 = () => x.val += 1; 
const x2 = () => x.val *= 2;

x1();
x2();
console.log(x.val); // 6

//however, if the call order was instead
x2();
x1();
console.log(x.val) // 5
```

### 2.3 Avoid mutating state

Immutability is a central concept of functional programming because without it, the data flow in your program is lossy. State history is abandoned, and strange bugs can creep into your software.

Mutation causes defects. If you have a dollar, and I give you another dollar, it does not change the fact that a moment ago you only had one dollar, and now you have two. Mutation attempts to erase history which can manifest as bugs in the program.

```js
let dollars = 1 // Mutable binding
dollars +=1
console.log(dollars) // 2 - history is lost
```

```js
const dollars = 1 // Immutable
const newDollars = dollars + 1
console.log(dollars) // 1 - history is preserved
// dollars +=1 // error
```

If you have ever dubgged code where you have tried to find where the object's value changed to `undefined` you'll appreciate _immutability_.

> This means that in FP there are no variables. There is no `var` or `let`, everything is a `const`. 

You'll mostly be creating copies of objects.

```js
// Bad
const save = obj => {
    obj.saved = true;
    return obj;
}

// Good
const save = obj => {
    let newObj = obj.clone();
    newObj.saved = true;
    return newObj;
}
```

In JavaScript, it’s important not to confuse `const`, with immutability. `const` creates a variable name binding which can’t be reassigned after creation. `const` does not create immutable objects. You can’t change the object that the binding refers to, but you can still change the properties of the object, which means that bindings created with const are mutable, not immutable.

Immutable objects can’t be changed at all. You can make a value truly immutable by deep freezing the object. JavaScript has a method that freezes an object one-level deep:

```js
const a = Object.freeze({
  foo: 'Hello',
  bar: 'world',
  baz: '!'
});

a.foo = 'Goodbye';
// Error: Cannot assign to read only property 'foo' of object Object
```

But frozen objects are only superficially immutable. For example, the following object is mutable:

```js
const a = Object.freeze({
  foo: { greeting: 'Hello' },
  bar: 'world',
  baz: '!'
});

a.foo.greeting = 'Goodbye';

console.log(`${ a.foo.greeting }, ${ a.bar }${a.baz}`); //Goodbye, world!
```

As you can see, the top level primitive properties of a frozen object can’t change, but any property which is also an object (including arrays, etc…) can still be mutated — so even frozen objects are not immutable unless you walk the whole object tree and freeze every object property.

There are several libraries for creating immutable objects. e.g. [Immutable.js](https://github.com/immutable-js/immutable-js)

> :pencil:  You won’t be able to have all of our variables immutable and this is okay!


## Can we write a program with only pure functions?

No. 

As per the rule of Pure Functions, every function that reaches out for the DOM or uses variables that are not in it’s scope is therefore considered impure. However, there will be needs to be able to work with the DOM, you need to be able to send requests to services or log messages in the console.

The goal of FP is to compose the majority of your program from small pieces of logic that can be combined together and reused. Side effects are inevitable but by limiting them to certain places in your application they will be easier to manage and track.

## 3 More FP Concepts

### 3.1 Reusability Through Higher Order Functions

JavaScript has first class functions, which allows us to treat functions as data — assign them to variables, pass them to other functions, return them from functions, etc. 

**Higher-order Functions** either take functions as parameters, return functions or both.

Functional programming tends to reuse a common set of functional utilities to process data.

```js
const add = (x, y) => x + y
const substract = (x, y) => x - y
const evaluate = (fn, x, y) => fn(x, y)

evaluate(add, 3, 2) // 5
evaluate(substract, 3, 2) // 1
```


 Object oriented programming tends to colocate methods and data in objects. Those colocated methods can only operate on the type of data they were designed to operate on, and often only the data contained in that specific object instance.

In functional programming, any type of data is fair game. JS does this by using Higher Order functions.

```js
evaluate(add, "Prateek ", "Sharma"); // Prateek Sharma
```
Another examples is the Array's map() function. The same map() utility can map over objects, strings, numbers, or any other data type because it takes a function as an argument which appropriately handles the given data type.

### 3.2 Declarative vs Imperative

Functional programming is a declarative paradigm, meaning that the program logic is expressed without explicitly describing the flow control.

**Imperative** programs spend lines of code describing the specific steps used to achieve the desired results — the flow control: **How** to do things.

**Declarative**   programs abstract the flow control process, and instead spend lines of code describing the data flow: **What** to do. The how gets abstracted away.


What does the code below do?

```js
const doubleMap = numbers => {
  const doubled = [];
  for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
  }
  return doubled;
};

console.log(doubleMap([2, 3, 4])); // [4, 6, 8]
```
It takes some time to answer this questions because program is *imperative*. Contract it to a more *declarative* style below

```js
const doubleMap = numbers => numbers.map(n => n * 2);

console.log(doubleMap([2, 3, 4])); // [4, 6, 8]
```

This code uses `map()` function from the `Array` prototype and abstract the flow control to express the clearly the data flow instead.

**Imperative** code frequently utilizes statements. A statement is a piece of code which performs some action. Examples of commonly used statements include for, if, switch, throw, etc…

**Declarative** code relies more on expressions. An expression is a piece of code which evaluates to some value. Expressions are usually some combination of function calls, values, and operators which are evaluated to produce the resulting value. e.g.

```js
2 * 2
doubleMap([2, 3, 4])
Math.max(4, 3, 2)
```

### 3.3 Functional Composition

Function composition is the act of combining multiple functions together in order to create more complex logical flows. 

> The precondition that we need to have in mind is that when we compose functions each one should take the output from the previous. 

If we had to add two numbers and then multiply the result of the addition by 10, we might right something like this

```js
const sumNumbers = (a, b) => a + b
const multiply10 = x => x * 10

const result = multiply10(sumNumbers(3, 5)) // 10 * (3 + 5)
```

The code is easy to understand but if we had to perform more operations readability suffers. 

```js
const sum = (a, b) => a + b
const square = x => x * x
const addTen = x => x + 10

const computeNumbers = addTen(square(sum(3, 5))) // 74
```

We can use `Ramda` which is a JS library to enable us to easily write functional code.

```JS
const R = require('ramda')
const compute = R.compose(
  addTen,
  square,
  sum
)

compute(3, 5) // 74
```

### 3.4 Currying

A Curried Function is a function that only takes a single parameter at a time. So Currying is a technique of taking a function which takes multiple arguments and transforming them into chained functions which each take a single argument.

```js
// The non-curried version takes all arguments it needs and returns the result
const add = (x, y) => x + y
add(2, 3) // 5

// The curried function takes the arguments one by one and executes when it
// has received the last one. Each invocation before that will result in a new
// function being returned which will take the next argument from the chain.
const addCurried = x => y => x + y
const addTwo = addCurried(2) // function
const result = addTwo(3) // 5
```

The `curriedAdd` function first takes an argument x and it returns a function. It does not do any calculations at this point. The function that gets returned takes an argument y and returns the sum of y with x from the previous function.

ES5 syntax makes it more clear
```js
const add = function (x) {
  return function(y) {
    return x + y
  }
}
add(2)(3); // 5
```
> so Currying is basically converting f(x, y) into f(x)(y).

Currying shines during refactoring when you create a generalized version of a function with lots of parameters and then use it to create specialized versions with fewer parameters. This way we can write more descriptive code and achieve much better code reusability.

```js
// Generic curried function to make a Fetch() request
function makeRequest(type) {
  return function(url) {
      return function(data) {
        return fetch(url, { type, data })
      }
  }
}


// Returns the first function which will take the url.
const makePostRequest = makeRequest('POST');

// Returns the final function which when called with some data will send a POST request.
const saveArticle = makePostRequest('http://example.com/article');

// We get the result from the saving by calling saveArticle with the article object
const result = saveArticle({ title: 'Currying is so good!' });
```

**Ramda** has `curry` method using which we have curry and existing method.
```js
const R = require('ramda') 

const add = (x, y) => x + y
const addCurried = R.curry(add)
const addTwo = addCurried(2)

addTwo(5)  // 7

// Also, we can still call the curried function by passing two arguments

addCurried(2, 5) // 7
```

## Language Constructs that will get thrown away

A good first step towards writing good Functional code is to stop using the below JS constructs as they break one or more FP principles.

- Loops                                         // Loops lead to imperative code.
  - while
  - do...while
  - for
  - for...of
  - for...in
- Void functions                                // A function that does not return anything can only cause side-effects and thus is Impure.
- Variable declarations with `var` or `let`     // Mutation of state.
- Object mutation (for example: `o.x = 5;`)     // Mutation of state.
- Array mutator methods                         // Mutation of state.
  - copyWithin
  - fill
  - pop
  - push
  - reverse
  - shift
  - sort
  - splice
  - unshift
- Map mutator methods                           // Mutation of state.
  - clear
  - delete
  - set
- Set mutator methods                           // Mutation of state.
  - add
  - clear
  - delete

## Resources

[ImmutableJS](https://immutable-js.github.io/immutable-js/docs/#/)

[RamdaJS](https://ramdajs.com/)

[ESLint FP Plugin](https://www.npmjs.com/package/eslint-plugin-fp)

[OO VS FP](https://medium.com/@cscalfani/)

[Goodby Object oriented programming](goodbye-object-oriented-programming-a59cda4c0e53)

[7 Reasons to use FP for Frontend](https://tsh.io/blog/7-reasons-to-use-functional-programming-on-frontend-1-2/)