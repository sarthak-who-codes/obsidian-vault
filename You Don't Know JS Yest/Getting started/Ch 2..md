## Each file is a program

Each file is a stand alone program is js. This is because of error handling. If one file fails, that will not necessarily prevent next file from being executed.

The only way multiple standalone .js files act as a single program is by sharing their state (and access to their public functionality) via the "global scope." They mix together in this global scope namespace, so at runtime they act as a whole.

**For example:**

**`file1.js`**

```js
var globalVar = "Hello from file1.js";

function greet() {
    console.log(globalVar);
}
```

**`file2.js`**


```js
console.log(globalVar); // Accessible from file1.js
greet(); // Works fine because it's globally available
```

**HTML File**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Global Scope Example</title>
</head>
<body>
    <script src="file1.js"></script>
    <script src="file2.js"></script>
</body>
</html>
```


Here, `file2.js` can access `globalVar` and `greet()` because they exist in the global scope after `file1.js` is loaded.


With ES6, JavaScript introduced **modules**, where each file is treated as an isolated module unless it explicitly exports and imports values.

Modules are also file-based. If a file is loaded via module-loading mechanism such as an `import` statement or a `<script type=module>` tag, all its code is treated as a single module.

**Example:**

**`module1.js`**

```js
export const message = "Hello from module1.js";

export function greet() {
    console.log(message);
}

```

**`module2.js`**

```js
import { message, greet } from "./module1.js";

console.log(message); // Works because it was imported
greet(); // Works as well

```

**HTML File**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>ES6 Modules Example</title>
</head>
<body>
    <script type="module" src="module2.js"></script>
</body>
</html>
```

Though you wouldn't typically think about a module—a collection of state and publicly exposed methods to operate on that state—as a standalone program, JS does in fact still treat each module separately. Similar to how "global scope" allows standalone files to mix together at runtime, importing a module into another allows runtime interoperation between them.

---

## Values

Values are data. They're how the program maintains state. Values come in two forms in JS: **primitive** and **object**.

Basically same as: [[2. Fundamentals]]

### Primitive vs Non-primitive data types

- Unlike primitive values, non-primitive values are referenced.
- Reference types store a **memory address (reference)** pointing to the actual data. When assigned to a variable, the reference (not the actual value) is copied, meaning changes affect all variables pointing to the same object.
	- `Object`
	- `Array`
	- `Function`

```js
// Primitive
let a = 10;
let b = a; // Copy of `a` is assigned to `b`
b = 20;

console.log(a); // 10 (remains unchanged)
console.log(b); // 20

// Non primitive
let obj1 = { name: "Sarthak" };
let obj2 = obj1; // Both `obj1` and `obj2` point to the same object

obj2.name = "Roy";

console.log(obj1.name); // "Roy" (changed because obj1 and obj2 share the same reference)
console.log(obj2.name); // "Roy"

```

### Type determination

**`type of` operator:** For distinguishing values, the `typeof` operator tells you its built-in type, if primitive, or `"object"` otherwise:

```js
typeof 42;                  // "number"
typeof "abc";               // "string"
typeof true;                // "boolean"
typeof undefined;           // "undefined"
typeof null;                // "object" -- oops, bug!
typeof { "a": 1 };          // "object"
typeof [1,2,3];             // "object"
typeof function hello(){};  // "function"
```

| WARNING:                                                                                                                                                                                    |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `typeof null` unfortunately returns `"object"` instead of the expected `"null"`. Also, `typeof` returns the specific `"function"` for functions, but not the expected `"array"` for arrays. |

---
## Variables

- `var` is global scoped while `let` and `const` are block scoped
- `const` are **"mutable"** but not **"reassignable"**.

### Functions

**Definition:** A set of statements that can be executed multiple times, take inputs (parameters), and return outputs (values).

- Can be stored inside objects as methods.
- In, JS functions are first class citizens which means, functions can be assigned to variables, passed to and returned from other functions, stored in arrays.


1. **Function Declarations:** Defines a function using the `function` keyword, followed by the function's name:

```js
function awesomeFunction(coolThings) {
    // Function body
    return amazingStuff;
}
```

This is called a **function declaration** because:

- It stands alone as a statement.
- The function is **hoisted**

2. **Function expression:** Unlike declarations, **function expressions** assign functions to variables.

```js
var awesomeFunction = function(coolThings) {
    // Function body
    return amazingStuff;
};

```

- Here, `awesomeFunction` holds an **anonymous function**.
- Function expressions are **not hoisted**.

---
## Comparison

### Strict equality operator

Checks both values and types. No type conversion is performed.

```js
3 === 3.0;              // true
"yes" === "yes";        // true
null === null;          // true
false === false;        // true

42 === "42";            // false
"hello" === "Hello";    // false
true === 1;             // false
0 === null;             // false
"" === null;            // false
null === undefined;     // false
```

**Info bit:** There are few instance where `===` is designed to lie

```js
NaN === NaN;            // false
0 === -0;               // true
```

**Reasons:**

1. **`NaN === NaN`:** JS follows the IEEE 754 floating-point standard, which states that **NaN is never equal to NaN**.
2. **`0 === -0`:** JS follows the IEEE 754 floating-point standard, where **positive zero (`0`) and negative zero (`-0`) are considered equal**

**How to cope:**

1. **`Number.isNaN()`:**

```js
console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN(42));  // false
```

2. **``Object.is()``:**

```js
console.log(Object.is(0, -0)); // false
console.log(Object.is(0, 0));  // true
console.log(Object.is(-0, -0)); // true
```

#### Comparison of Non-primitive data types

```js
[ 1, 2, 3 ] === [ 1, 2, 3 ];    // false
{ a: 42 } === { a: 42 }         // false
(x => x * 2) === (x => x * 2)   // false
```

JS does not define `===` as _structural equality_ for object values. Instead, `===` uses _identity equality_ for object values.

In JS, all object values are held by reference (see "Values vs References" in Appendix A), are assigned and passed by reference-copy, **and** to our current discussion, are compared by reference (identity) equality. Consider:

```js
var x = [ 1, 2, 3 ];

// assignment is by reference-copy, so
// y references the *same* array as x,
// not another copy of it.
var y = x;

y === x;              // true
y === [ 1, 2, 3 ];    // false
x === [ 1, 2, 3 ];    // false
```

There is no way to check structural equality in JS.

### Coercive comparison

Value of one type being converted to its respective representation in another type before comparison.

- Coercion is core pillar of JS and the major reason behind hate.
- `==` is same as `===`. It's just that `==` performs type conversions.
- `==` is described as loose equality but in reality its **"coercive equality"**.

```js
42 == "42";             // true
1 == true;              // true
```

**Gotchas of `==`:** It's endless. Follow [ChatGPT](https://chatgpt.com/share/67c098db-86c0-8004-bb3e-2495d74879d4)

---
## Organize JS

1. **Class:** Object oriented pattern
2. **Module:** 
	1. **Classic modules:** These are just a outer function.
	2. **ES Modules:** Each file is a module.