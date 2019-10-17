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


## Test Patterns

### No more Page Objects

Page Object design pattern has two benefits
1. They keep all page element selectors in one place and thus separation of Test code from Locators of the AUT
1. They standardize how tests interact with the page and thus avoid duplication of code and ease code maintenance.

OO in JS is a little awkward. Introduction on `Class` in ES6 helps but Classes, specifically `this` keyword, can still surprise people used to Java because they work differently than. [Here is a great blog from Kent C. Dodds which highlights this point](https://kentcdodds.com/blog/classes-complexity-and-functional-programming)

### Enter Page Modules

In Java land it's pretty common to find Page Objects which inherit from the Base Page. e.g. of this in JS will be

```JS
import { HomePage } from './BasePage'

class HomePage extends BasePage  {
  constructor() {
    super();
    this.mainElement = 'body > .banner';
  }

//... More code

export const mainPage = new MainPage();

}
```

With the move to FP we are going to loose not only Inheritance but the Class itself. Therefore we need to use `Modules` to arrange our code. Each  `module` exports public functions that can be imported into other modules and used.

```js
// HomePage Module  - HomePage.js
export function login(email, password){
  //...
}

export function logout(){
  //...
}

export function search(criterion){
  // ...
}
```

This module could be then imported into your tests or other modules and used as below.
```js
// HomePage Test - HomePageTest.js
import * as homePage from './HomePage.js'

describe('Home Page', ()=> {
  it('User can login', ()=> {
      cy.visit('/')
      homePage.login('prateek','123456')
  })
})

```

or We could selectively import individual functions from a module

```js
import {login} from './HomePage.js'
describe('Home Page', ()=> {
  it('User can login', ()=> {
    cy.visit('/')
    login('prateek','123456')
  })
})

```

#### What about Inheritance

```java
public class HomePage extends BasePage {

}

```
A lot of times we come around Test suites where Page Objects extend a `BasePage` or every test file extends a `BaseTest` class. The intention behind doing this is often code reuse. Most often the `BaseTest` class has methods related to login, logout and logging etc. Please don't do that. Bundling unrelated functionality into a parent class for the purpose of reuse is an abuse of Inheritance.

Common functionality that is to be made available to Specs could be added as Cypress `Custom commands`. Custom Commands are available to be used globally with the `cy.` prefix. e.g. we can add a method called `login` as a custom command as below.

```js
Cypress.Commands.add('login', (username, password) => {
    cy.get('#username').type(username)
    //...
})
```

The `Cypress.Commands.add` takes the name of the custom command as the first argument and a closure as the second argument.

Now we could use the custom command in any spec.

```js
describe('Login Page', ()=>{
  it('User can login', ()=>{
    cy.login('prateek','123456')
    // ...
  })
})

```

Functionality that is shared between few Specs but not all should be added to utility modules. 

##### Favour composition over Inheritance

Why? Watch this video

<iframe width="560" height="315" src="https://www.youtube.com/embed/wfMtDGfHWpA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Consider the below code which uses Inheritance 

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
  getInfo(greetStr) {
    return `${greetStr}, I am ${this.firstName} ${this.lastName}.`;
  }
}

class Employee extends Person {
  constructor(firstName, lastName, employeeId) {
    super(firstName, lastName);
    this.employeeId = employeeId;
  }

  getId(greetStr) {
    return `${greetStr}, my employee id is ${this.employeeId}.`;
  }
}
const employee = new Employee('John', 'Doe', 123);
console.log(employee.getInfo('Hi')); // Hi, I am John Doe.
console.log(employee.getId('Hello')); // Hello, my employee id is 123.
```

The same functionality could be achieved using Composition like so

```js
// We first define all the functions that the classes will have included e.g. getInfo() and getId()

function getInfo(firstName, lastName, greetStr){
  return `${greetStr}, I am ${firstName} ${lastName}.`
}

function getId(emplyeeId, greetStr){
  return `${greetStr}, my employee id is ${emplyeeId}.`;
}

// Instead of a Person Class we create a function which returns an Object which represents the person. This object is "Composed" of the bindings and functions available to us.

function CreatePerson(firstName, lastName){
  return {
    firstName: firstName,
    lastName: lastName,
    getInfo: (greetStr) => getInfo(firstName, lastName, greetStr) 
  }
}
 
function CreateEmployee(firstName, lastName, employeeId){
  return {
    employeeId: employeeId,
    getId : (greetStr) => getId(employeeId, greetStr),
    getInfo: (greetStr) => getInfo(firstName, lastName, greetStr) 
  };
}

// Notice that the objects returned by CreatePerson and CreateEmployee functions are independent of each other(i.e. not bounded by a relation aka inheritance). Both the returned objects have a property on them who's value happens to be the same function.

let person = CreatePerson('Bla', 'Bla')
let employee = CreateEmployee('John', 'Doe', 123)
console.log( employee.getInfo('Hi')) // Hi, I am John Doe.
console.log( employee.getId('Hello')) // Hello, my employee id is 123.
```

Functions which return objects are called `Factory functions`. Watch the below video for simpler and more convinsing argument for using *Factory Functions* over classes.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ImwrezYhw4w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


#### Readability

The main benefit of using Page Object is that it encapsulates the complexity of the UI and locators and thus helping with reusability and making the tests more readable.

> For these examples I am using the [Cypress TodoMVC Example Repo](https://github.com/cypress-io/cypress-example-todomvc) and refactoring few tests.

Compare the below tests
```js
describe('Todo Application', ()=> {

  it('Can add a new Todo', ()=>{
     cy.get('.new-todo')
      .type('First Todo')
      .type('{enter}') 

     cy.get('.todo-list li')
      .eq(0)
      .find('label')
      .should('contain', 'First Todo')  
  })
})

```

VS

```js
import {addTodo, getTodoName} from './TodoUtil'

describe('Todo Application', ()=> {

  it('Can add a new Todo', ()=>{
     addTodo('First Todo')
     .then(getTodoName)
     .should('equal', 'First Todo')
  })
})

```

The second test requires less cognitive load to understand because it's declarative and don't makes us read through the steps of how to add new todo or get it's name.

The `addTodo` and `getTodoName` come from the `TodoUtil` module

```js
// TodoUtil.js
export const addTodo = (name) => {
  cy.get('.new-todo')
    .type('First Todo')
    .type('{enter}') 

  return cy
    .get('.todo-list')
    .eq(0)
}

export const getTodoName = (todo) => {
  return todo.find('label)
}
```

While the first approach is fine for small and simple scenarios but as the scenarios become more complex or longer a more declarative approach can be a lifesaver.

```js
// The scenario to test that we are able to update the newly created Todo will look like
import {addTodo, getTodoName, updateTodo} from './TodoUtil'

describe('Todo Application', ()=> {

  const TODO_NAME = 'First todo'

it('Can update a newly created todo', ()=>{
    addTodo(TODO_NAME)
    .then(updateTodo(TODO_NAME + ' new'))
    .then(getTodoName)
    .should('equal', TODO_NAME + ' new')

  })
})

```

The new method `updateTodo` from `TodoUtils.js` looks like

```js
export const updateTodo = (name) => ($todo) => {
  cy.wrap($todo).within(() => {
    cy.get('label').dblclick()
    cy.get('.edit').clear().type(`${name}{enter}`)
  })

  return cy.wrap($todo)
```

### But I still love my Page Objects.

Most common arguments against Page Objects are 

1. Page Objects introduce additional state in addition to the AUT's state which makes tests hard to understand.
1. Using Page object means that all our tests are going to go through the Application's GUI.
1. Page objects try to fit multiple cases into a uniform interface, falling back to conditional logic.

While the above arguments are all true in my experience, but biggest problem with Page Objects arise due to Selenium's recommendation that "methods should return Page Objects". 

[Read all recommendations here](https://github.com/SeleniumHQ/selenium/wiki/PageObjects)

Let's try to look at some common situations we find ourselves in when using Page Objects and how to solve them.

#### Single Responsibility Principle is not met by Page Objects
PO bind unrelated functionality together in one class. e.g. in the below code `searchProduct()` functionality is not related to `login` or `logout` actions.

```java
public class HomePage {
    private final WebDriver webDriver;

    public HomePage(WebDriver webDriver) {
        this.webDriver = webDriver;
    }

    public SignUpPage signUp() {
        webDriver.findElement(By.linkText("Sign up")).click();
        return new SignUpPage(webDriver);
    }

    public void logOut() {
        webDriver.findElement(By.linkText("Log out")).click();
    }

    public LoginPage logIn() {
        webDriver.findElement(By.linkText("Log in")).click();
        return new LoginPage(webDriver);
    }
    
    public ProductPage searchProduct(String product){
        webDriver.findElement(By.linkText(product)).click();
        return new ProductPage(webDriver);
    }
    
}

```

This means that or class does not follow Single Responsiility Princial (SPR).

> The **Single Responsibility Principal (SRP)** states that every module or class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class.

Breaking Page Objects like above into multiple smaller Page Objects does not help with `SRP` either. 

e.g. We could take the `Login` action outside the `HomePage` and create a new `LoginPage` object and use it like below.

```Java
LoginPage loginPage = new HomePage().navigateToLoginPage();
loginPage.login("username", "password");
``` 

As the actions belong to 2 different pages, this code will repeat in all test cases that use login. The responsibility is not entirely encapsulated.

One way of fixing this is be to define our Class/Module not by the page but by the intent.

```js
// login.js

export const loginAsCustomer = (name, password) => {
}

```
The `loginAsCustomer` method can then work through both the Home and Login screens of the application to complete login as a single user action.

> :pencil: If possible, prefer basing your modules on user intent rather than basing them strictly by Page.

#### Page Order != User Flows

Another place where PO may complicate things is when User flows are not be same as the page order. 

Consider the example of a shopping website. Here the user can add an item to the cart either using the Product Page or using the search functionality on the Search page. 

From the Cart page the user maybe taken to either the Home page or the Search page on clicking "continue to shop", depending on if the last item was added using the Product Page or the Search Page.

The code for the `CartPage` class might now look something like this 

```java
public class CartPage{       
    Page continueShopping(){
     if(state) {  // determines using which page the last item was added
       return new SearchPage();
     }
     else {
       return new HomePage();
     }    
}

```

**Why is this a problem?**

Not only is this code more complex to understand and we have to maintain additional state, it also makes it harder to modify the CartPage if in future another user flow was introduced. This violates the _Open/Closed principle (OCP)._

> **The open/closed principle(OCP)** states “software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification”

One way to remove the state logic from our `Cart` Module is by changing the `continueShopping` method to not return references to other Classes/Modules and limit it to only clicking the continue shopping link.

```js
// cart.js

export const continueShopping(){
  cy.get('#continue').click();
}

// Test.js

it('user can add item to cart from home page post selecting "continue to shop"', (){
    //.... code to add product to the cart from Product Page
    cartPage.continueShopping();
    homePage.navigateToProductPage();
    productPage.addItemToCart('item2');
})

it('user can add item to cart from search page post selecting "continue to shop"', (){
    //.... code to add product to the cart using Search
    cartPage.continueShopping();
    searchPage.addItemToCart('item');
})
```

In this example our Test builds user flows just by choosing the right order of calling loosely coupled steps. This means our individual modules did not have to maintain state and state based was removed.

#### Loosely coupled steps

Need another example of how loosely coupled steps reduce complexity? Consider the below typical `LoginPage` class.

Business requirement is that on successful login use is taken to "Home Page" and on unsuccessful login use stays on the "Login" page.

```java
class LoginPage {
    HomePage validLogin(String userName,String password){...}
    LoginPage invalidLogin(String userName,String password){...}
  }
}
```

Now let's introduce roles in the mix. An "Admin" on login is taken to the "Admin Dashboard" instead of the Home Page. So we now need to add another method to the LoginPage class and return an instance of the "Admin Dashboard" Page.

```java
class LoginPage {
    HomePage validLogin(String userName,String password){...}
    LoginPage invalidLogin(String userName,String password){...}

    AdminDashboardPage adminValidLogin(String userName,String password){...}
  }
}
```

More roles will mean even more methods because there is a tight coupling between the pages and the return type. 

Let's fix this by not returning references to different pages by the login action.

```js
// login.js
export default const login = (username, password) => {
  cy.get('.username').type(username)
  cy.get('.password').type(password)
  cy.click('.loginButton')
}
```

Our test will now look like

```js
// Test.js
it('User is taken to Home Page on valid login', ()=> {
   login('prateek', '12345')
   cy.title().should('equal', 'Home Page');
})

it('Admin is taken to Admin Dashboard on valid login', ()=> {
   login('admin', '12345')
   cy.title().should('equal', 'Admin Dashboard');
})

```

Hopefully you can see that preferring Loosely Coupled steps leads to us writing less lines of code with reduced complexity.

> :bulb: ASS tests in NIB when through a big refactor 2 years back to not have methods return Page Objects.

## Resources

[ImmutableJS](https://immutable-js.github.io/immutable-js/docs/#/)
[RamdaJS](https://ramdajs.com/)
[ESLint FP Plugin](https://www.npmjs.com/package/eslint-plugin-fp)

[OO VS FP](https://medium.com/@cscalfani/)
[Goodby Object oriented programming](goodbye-object-oriented-programming-a59cda4c0e53)
[7 Reasons to use FP for Frontend](https://tsh.io/blog/7-reasons-to-use-functional-programming-on-frontend-1-2/)

## Sources

https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0

https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-currying-5652e489cce8



https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-3-1b0fd14eb1a7

https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-pure-functions-797d9abbee1

https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-currying-5652e489cce8

https://tsh.io/blog/7-reasons-to-use-functional-programming-on-frontend-1-2/