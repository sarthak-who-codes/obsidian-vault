## Backwards and Forwards compatibility

**Backwards compatibility** ensures that once JavaScript code is valid, it remains valid in future versions. Code from 1995 should still work today, as breaking changes could disrupt the web. This principle makes JavaScript a reliable choice but also limits the ability to remove or change flawed features.

**Forwards compatibility**, on the other hand, means new features won’t break execution in older environments. JavaScript **is not** forwards-compatible, unlike HTML and CSS, which gracefully ignore unknown elements.


## Transpilling

Transpiling is a contrived and community-invented term to describe using a tool to convert the source code of a program from one form to another (but still as textual source code). 

Typically, forwards-compatibility problems related to syntax are solved by using a transpiler (the most common one being Babel ([https://babeljs.io](https://babeljs.io/))) to convert from that newer JS syntax version to an equivalent older syntax.

For example, a developer may write a snippet of code like:

```js
if (something) {
    let x = 3;
    console.log(x);
}
else {
    let x = 4;
    console.log(x);
}
```

The Babel transpiler might convert that code to look like this:

```js
var x$0, x$1;
if (something) {
    x$0 = 3;
    console.log(x$0);
}
else {
    x$1 = 4;
    console.log(x$1);
}
```

The original snippet relied on `let` to create block-scoped `x` variables in both the `if` and `else` clauses which did not interfere with each other. An equivalent program (with minimal re-working) that Babel can produce just chooses to name two different variables with unique names, producing the same non-interference outcome.

## Polyfill

While tools like <u>Babel</u> can easily handle syntax changes (eg. new `let` and `const` to `var`), problem arises when new APIs are added to JS.

In that case polyfill comes to play which defines the new method.

For example, the `.finally(...)` block was added in ES2019 but if it runs on a pre-ES2019 environment, it would occur and error.

A polyfill for `finally(..)` in pre-ES2019 environments could look like this:

```js
if (!Promise.prototype.finally) {
    Promise.prototype.finally = function f(fn){
        return this.then(
            function t(v){
                return Promise.resolve( fn() )
                    .then(function t(){
                        return v;
                    });
            },
            function c(e){
                return Promise.resolve( fn() )
                    .then(function t(){
                        throw e;
                    });
            }
        );
    };
}
```

Explanation of the code: [ChatGPT](https://chatgpt.com/share/67be958f-c714-8004-9810-0b1aa1016caa), [DeepSeek](https://chat.deepseek.com/a/chat/s/b102ffb3-4189-4fb3-bfe1-6c7941d379b2)

The `if` statement protects the polyfill definition by preventing it from running in any environment where the JS engine has already defined that method. In older environments, the polyfill is defined, but in newer environments the `if` statement is quietly skipped.

Transpilers like <u>Babel</u> typically detect which polyfills your code needs and provide them automatically for you. But occasionally you may need to include/define them explicitly, which works similar to the snippet we just looked at.

## Interpretation

There are mainly 2 types of languages.

1. **Interpreted Languages**:
    - Traditionally, interpreted languages execute code line-by-line, top-down.
    - Errors are discovered during execution, which means a program might partially run before encountering an error.
    - Examples include older scripting languages like early versions of Python or shell scripts.

2. **Compiled Languages**:
    - Compiled languages undergo a processing step (parsing) before execution.
    - Syntax errors are caught during this phase, preventing any execution if errors are present.
    - The parsed code is often converted into an intermediate or binary form before execution.
    - Examples include C, C++, and Java.


JS in none of the above. JS in **JIT( Just-In-Time )** compiled language( Rewatch Akshay Saini's video ).

#### Working of JS

1. **Parsing**: JS is first parsed into an **Abstract Syntax Tree (AST)**. This step is necessary for detecting early syntax errors.
2. **Compilation**: Instead of direct execution, JS is **compiled into an intermediate bytecode representation** (IR) for optimization.
3. **Execution**: The JS **virtual machine (VM) executes the optimized bytecode**, often using **Just-In-Time (JIT) compilation** to further improve performance.

[![Image](https://raw.githubusercontent.com/getify/You-Dont-Know-JS/refs/heads/2nd-ed/get-started/images/fig2.png)](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/get-started/images/fig2.png)
In this processing model, an invalid command (such as broken syntax) on line 5 would be caught during the parsing phase, before any execution has begun, and none of the program would run.

JS is closer to being a compiled language than being a interpreted langage.

## Web Assembly

- Successor of ASM.js
- Allows non-JS languages to run in browser.
- **Ahead-of-time (AOT) compilation** for better performance.
- Reduces pressure to add features to JS that only benefit transpiled languages.
- Evolving into a **cross-platform virtual machine (VM)** which can be run outside of browser.

---
## Strict mode

- In ESM, strict mode enabled by default.
- Most strict mode controls are in the form of _early errors_, meaning errors that aren't strictly syntax errors but are still thrown at compile time (before the code is run).
- In strict mode `this` defaults to `undefined`.
- `"use strict"` has to at the top. Only comments can be above it.
- Strict mode can be enabled per function basis.

```js
function someOperations() {
    // whitespace and comments are fine here
    "use strict";
    // all this code will run in strict mode
}
```

- If a file has strict mode turned on, the function-level strict mode pragmas are disallowed. So you have to pick one or the other.