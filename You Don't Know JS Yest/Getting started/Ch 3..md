## Iteration

A way to sequentially access elements in a collection without exposing the underlying details of how the data is stored.

- "One piece at a time" approach
- Independent of any data structure / source. Provides cleaner logic.

ES6 standardize a method that  defines a `next()` method whose return is an object called an _iterator result_; the object has `value` and `done` properties, where `done` is a boolean that is `false` until the iteration over the underlying data source is complete.

### Consuming Iterators

#### 1. Manual way

```js
const numbers = [10, 20, 30];
const iterator = numbers[Symbol.iterator](); // Get the iterator

console.log(iterator.next()); // { value: 10, done: false }
console.log(iterator.next()); // { value: 20, done: false }
console.log(iterator.next()); // { value: 30, done: false }
console.log(iterator.next()); // { value: undefined, done: true }

```

#### 2. `for...of` loop

```js
// given an iterator of some data source:
var it = /* .. */;

// loop over its results one at a time
for (let val of it) {
    console.log(`Iterator value: ${ val }`);
}
// Iterator value: ..
// Iterator value: ..
// ..
```

#### 3. Spread operator( ... )

Array spread:

```js
// spread an iterator into an array,
// with each iterated value occupying
// an array element position.
var vals = [ ...it ];
```

Function call spread: //Why this is iteration?

```js
// spread an iterator into a function,
// call with each iterated value
// occupying an argument position.
doSomethingUseful( ...it );
```

### **Itearables**

**Definition:** It is a value that can be iterated over.

*"The iterator-consumption protocol automatically creates an iterator instance from an iterable, and consumes _just that iterator instance_ to its completion."*

Iterables data structures in JS are:
1. Array( `[]` )
2. Map( `new Map()` )
3. Set( `new Set` )
4. Strings( `""` )
5. Typed Arrays
6. NodeLists

**Quick Examples:**

1. **Array**

```js
// Using for...of loop
var arr = [ 10, 20, 30 ];

for (let val of arr) {
    console.log(`Array value: ${ val }`);
}


// Spread operator
var arrCopy = [ ...arr ];


// Using keys
const arr = ["apple", "banana", "cherry"];

for (let key of arr.keys()) {
    console.log(key);
}
// Output:
// 0
// 1
// 2


// Using values
const arr = ["apple", "banana", "cherry"];

for (let value of arr.values()) {
    console.log(value);
}
// Output:
// apple
// banana
// cherry

// Using .entries()
const arr = ["apple", "banana", "cherry"];

for (let [index, value] of arr.entries()) {
    console.log(`${index}: ${value}`);
}
// Output:
// 0: apple
// 1: banana
// 2: cherry

```

2. **Strings:**

```js
var greeting = "Hello world!";
var chars = [...greeting]; 

```

3. **Maps:**

```js
// Key-value pair method
var buttonNames = new Map();
buttonNames.set(btn1, "Button 1");
buttonNames.set(btn2, "Button 2");

for (let [btn, btnName] of buttonNames) {
    btn.addEventListener("click", () => console.log(`Clicked ${btnName}`));
}


// Only values
for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Output:
// Button 1
// Button 2


// Only keys
for (let btn of buttonNames.keys()) {
    console.log(btn);
}
// Output: btn1, btn2 (actual button elements)


// using .entries(); same as default for..of
for (let [btn, btnName] of buttonNames.entries()) {
    console.log(btn, btnName);
}
```


**Info bit:** For the most part, all built-in iterables in JS have three iterator forms available: keys-only (`keys()`), values-only (`values()`), and entries (`entries()`). For custom data structures, if it follows the iteration protocol, then it can be consumed using `for...of` loop.

---
## Closure

**Definition:** Closure is when a function remembers and continues to access variables from outside its scope, even when the function is executed in a different scope.

- Closure does **not** just capture a snapshot of variable values; it maintains a **live link** to those variables, meaning closure can actually observe (or make!) updates to these variables over time.
- Closure is most common when working with asynchronous code, such as with callbacks.
- **Condition:**
	1. A function is defined inside another function.
	2. The inner function "remembers" variables from its outer function even after the outer function has finished execution.

**Examples:**

- Basic example:

```js
function greeting(msg) {
    return function who(name) {
        console.log(`${ msg }, ${ name }!`);
    };
}

var hello = greeting("Hello");
var howdy = greeting("Howdy");

hello("Kyle");
// Hello, Kyle!

hello("Sarah");
// Hello, Sarah!

howdy("Grant");
// Howdy, Grant!
```

- Example of preservation of variable itself:

```js
function counter(step = 1) {
    var count = 0;
    return function increaseCount(){
        count = count + step;
        return count;
    };
}

var incBy1 = counter(1);
var incBy3 = counter(3);

incBy1();       // 1
incBy1();       // 2

incBy3();       // 3
incBy3();       // 6
incBy3();       // 9
```

**Info bit:** It's not necessary that the outer scope be a function—it usually is, but not always—just that there be at least one variable in an outer scope accessed from an inner function:

```js
for (let [idx,btn] of buttons.entries()) {
    btn.addEventListener("click",function onClick(){
       console.log(`Clicked on button (${ idx })!`);
    });
}
```

Because this loop is using `let` declarations, each iteration gets new block-scoped (aka, local) `idx` and `btn` variables; the loop also creates a new inner `onClick(..)` function each time. That inner function closes over `idx`, preserving it for as long as the click handler is set on the `btn`. So when each button is clicked, its handler can print its associated index value, because the handler remembers its respective `idx` variable.

---
## `this` keyword

In JavaScript, `this` is a special keyword that refers to the **execution context** of a function, meaning the object that the function is being executed on. Unlike variables, which are resolved based on **lexical scope**, `this` is determined at **runtime**, depending on how the function is called.

Two important concepts need to clear:

1. **Scope:** Scope is a static concept in JavaScript. It determines what variables a function has access to based on where the function is defined. Scope is established at the time a function is **declared** and does not change.
2. **Execution Context:** Execution context, is dynamic and is determined at the moment a function is **called**. Every function in JavaScript is executed within an execution context, and `this` is a reference to the object that **invokes** the function.

- Scope is static, but `this` is dynamic.

#### Process of determining `this`

1. **When a function is normally called:** 

`this` refers to global context and `undefined` in strict mode.

```js
function study() {
    console.log(this);
}

study(); // Logs the global object (window in browsers)

```

2. **Object method:** 

When a function is called as a method of an object, `this` refers to the **object that owns the method**.

```js
var student = {
    name: "Alice",
    study: function () {
        console.log(this.name);
    }
};

student.study(); // Output: Alice
```

3. **Using `call()`, `apply()`, and `bind()`:**

The `call()` method calls a function with a specified `this` value.

```js
function greet() {
    console.log(this.name);
}

var person = { name: "Bob" };

greet.call(person); // Output: Bob
```

`apply()` is similar to `call()`, but it takes an array of arguments.

```js
function introduce(language) {
    console.log(`${this.name} speaks ${language}`);
}

var speaker = { name: "Charlie" };

introduce.apply(speaker, ["English"]); // Output: Charlie speaks English

```

The `bind()` method does not immediately invoke the function but instead **returns a new function** with `this` permanently set.

```js
var user = {
    name: "David"
};

function sayHello() {
    console.log(this.name);
}

var boundHello = sayHello.bind(user);
boundHello(); // Output: David

```



---
---
### Extras

**Shallow copy:** A **shallow copy** creates a new object that has the same **top-level properties** as the original object, but **nested objects are still referenced**, not copied.

Shallow copy can be don using 

```js
const original = {
  name: "Alice",
  age: 25,
  address: { city: "New York", zip: 10001 },
};

// Shallow copy using spread operator
const shallowCopy = { ...original };

console.log(shallowCopy); 
// { name: 'Alice', age: 25, address: { city: 'New York', zip: 10001 } }

```

