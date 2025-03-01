### Basics

- `<script>` tag can not have both src and code inside.
- `type` tag in `<script>` is now used for javascript modules

---
### Code structure

- JavaScript by default adds `;` on a line break. But there are exceptions. For example it doesn't add `;` on a line break before parentheses `[...]` .
- Nested comments are not supported.

---
### "use strict"

- `"use strict"` have to be on the top of the file. Only comments can be placed before it.
- Modern javascript classes and modules by default enables them.

---
### Variables

- Variables are named storage for data
- To declare variables use: `let` and `var`(old way).
- To declare variable constant use: `const`
- **Variable name rule:**
	1. The name must contain only letters, digits, or the symbols `$` and `_`.
	2. The first character must not be a digit.
- **Uppercase constants:** When the values are hardcoded, use capital and underscore.

---
### Data types

#### Primitive data types

1. **Number:** Represents both integers and floating point numbers
	- There are other types of numbers called "special numeric values".
	    1. **Infinity:** Represents mathematical infinity.
	    2. **NaN:** Represents computational errors.
2. **BigInt:** Used to store numbers outside of the range of `±(253-1)`.
3. **String:** A string in JavaScript must be surrounded by quotes.
    1. `"Hello"`.
    2. `'Hello'`
    3. ``Hello``(backticks).
4. **Boolean:** The boolean type has only two values: `true` and `false`.
5. **The `null` value:** In JavaScript, `null` is not a “reference to a non-existing object” or a “null pointer” like in some other languages. It’s just a special value which represents “nothing”, “empty” or “value unknown”.
6. **The `undefined` value:** The meaning of `undefined` is “value is not assigned”.

***Tip:*** Normally, one uses `null` to assign an “empty” or “unknown” value to a variable, while `undefined` is reserved as a default initial value for unassigned things.

**Objects:** Collection of key-value pairs.

The `symbol` type is used to create unique identifiers for objects. We have to mention it here for the sake of completeness, but also postpone the details till we know objects.

[ChatGPT](https://chatgpt.com/share/67bdb66e-740c-8004-a676-4f563ac7633a)

---
### Interactions

#### alert
It shows a message and waits for user to press "OK".

```js
alert(``"Hello"``);
```

#### prompt
The function `prompt` accepts two arguments:

```javascript
result = prompt(title, [default]);
```

`title`

The text to show the visitor.

`default`

An optional second parameter, the initial value for the input field.

#### confirm
The function `confirm` shows a modal window with a `question` and two buttons: OK and Cancel.

```javascript
result = confirm(question);
```


---
### Type conversions

##### **String Conversion:** 
`String(value)` converts to string


#### **Numeric conversion:** 
Numeric conversion in mathematical functions and expressions happens automatically.
For example:

```js
alert( "6" / "2" ); // 3, strings are converted to numbers
```

If string is not valid number, expect `NaN`.

**Numeric conversion rules:**

| Value            | Becomes...                                                                                                                                                                                                                   |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `undefined`      | `NaN`                                                                                                                                                                                                                        |
| `null`           | `0`                                                                                                                                                                                                                          |
| `true and false` | `1` and `0`                                                                                                                                                                                                                  |
| `string`         | Whitespaces (includes spaces, tabs `\t`, newlines `\n` etc.) from the start and end are removed. If the remaining string is empty, the result is `0`. Otherwise, the number is “read” from the string. An error gives `NaN`. |

#### Boolean Conversion
Boolean conversion is the simplest one.

It happens in logical operations (later we’ll meet condition tests and other similar things) but can also be performed explicitly with a call to `Boolean(value)`.

The conversion rule:

- Values that are intuitively “empty”, like `0`, an empty string, `null`, `undefined`, and `NaN`, become `false`.
- Other values become `true`.

For instance:

```js
alert( Boolean(1) ); // true
alert( Boolean(0) ); // false
alert( Boolean("hello") ); // true 
alert( Boolean("") ); // false
alert( Boolean("0") ); // true`
alert( Boolean(" "`=`) ); // spaces, also true (any non-empty string is true)
```

***Tip:*** the string with zero `"0"` is `true`

| Value                                 | Becomes… |
| ------------------------------------- | -------- |
| `0`, `null`, `undefined`, `NaN`, `""` | `false`  |
| any other value                       | `true`   |

---
### Operators
- **Operands:** Is what operators applied to.
- **Operators:** Operations that are performed on operands.
- **Unary:** If there is a single operand(`x = -x`).
- **Binary:** If there are two operands(`y - x`).

#### Maths

The following math operations are supported:

- Addition `+`,
- Subtraction `-`,
- Multiplication `*`,
- Division `/`,
- Remainder `%`,
- Exponentiation `**`.

Read carefully: [Weird JS operations](https://javascript.info/operators#maths)

Weird exaples:
```js
const a = 1;
const b = 2;
typeof a + b; // Equivalent to (typeof a) + b; result is "number2"
```

---
