# The Complete JavaScript Guide

A comprehensive, in-depth tutorial covering JavaScript from first principles through advanced asynchronous programming and hoisting internals. Every topic below is explained thoroughly with code examples and conceptual diagrams.

## Table of Contents

1. [Introduction to JavaScript](#1-introduction-to-javascript)
2. [The Type System](#2-the-type-system)
3. [Objects & Arrays](#3-objects--arrays)
4. [Variables & Scope](#4-variables--scope)
5. [Functions](#5-functions)
6. [Control Flow](#6-control-flow)
7. [Array Higher-Order Methods](#7-array-higher-order-methods)
8. [Modern Syntax Features](#8-modern-syntax-features)
9. [Objects Deep Dive / OOP](#9-objects-deep-dive--oop)
10. [Closures & Execution Model](#10-closures--execution-model)
11. [Browser Runtime & DOM](#11-browser-runtime--dom)
12. [Asynchronous JavaScript — Introduction](#12-asynchronous-javascript--introduction)
13. [Modules & Ecosystem](#13-modules--ecosystem)
14. [Hoisting — Deep Dive](#14-hoisting--deep-dive)
15. [Asynchronous JavaScript — Deep Dive](#15-asynchronous-javascript--deep-dive)

---

## 1. Introduction to JavaScript

### 1.1 What JavaScript Is

JavaScript is a high-level, dynamically typed, garbage-collected programming language, originally built to add behavior and interactivity to web pages. "High-level" means you don't manage memory addresses or CPU registers directly — the engine handles that for you. "Dynamically typed" means a variable's type is determined by the value it currently holds, not by a fixed declaration. "Garbage-collected" means the engine automatically reclaims memory that is no longer reachable from your program.

JavaScript today runs far beyond the browser:

- **Browsers** — Chrome, Firefox, Safari, Edge
- **Servers** — Node.js
- **Mobile apps** — React Native
- **Desktop apps** — Electron
- **Build tools** — Vite, webpack
- **Edge/serverless environments** — Cloudflare Workers, Deno Deploy, etc.

A minimal program:

```js
const name = "Ilyass";
console.log("Hello " + name);
```

```
Hello Ilyass
```

**A critical distinction: JavaScript the language vs. the runtime environment.** `console.log()` is *not* part of the JavaScript language specification (ECMAScript). It is provided by whatever environment is running your code — the browser gives you `console`, `window`, `document`; Node.js gives you a different `console`, plus `fs`, `process`, etc. The language defines syntax, types, and semantics; the runtime supplies the surrounding APIs.

### 1.2 JavaScript vs HTML vs CSS

When building a web page, three technologies divide responsibility:

| Technology | Responsibility | Answers |
|---|---|---|
| HTML | Structure | "What exists on the page?" |
| CSS | Presentation | "How should it look?" |
| JavaScript | Behavior | "What should happen?" |

```html
<h1>Hello</h1>
<button>Click me</button>
```

```css
button {
    background: blue;
    color: white;
}
```

```js
button.addEventListener("click", () => {
    alert("Button clicked!");
});
```

Conceptually:

```
                 Web Application
                       │
        ┌──────────────┼──────────────┐
        │              │              │
       HTML           CSS       JavaScript
        │              │              │
    Structure       Styling        Logic
```

### 1.3 Where JavaScript Runs

Given:

```html
<script>
    console.log("Hello");
</script>
```

The browser's processing pipeline, at a high level:

```
HTML
 │
 ▼
Browser parses HTML
 │
 ▼
Finds <script>
 │
 ▼
JavaScript source code
 │
 ▼
JavaScript engine
 │
 ▼
Execute JavaScript
```

Every modern browser embeds a JavaScript engine:

| Browser | Engine |
|---|---|
| Chrome | V8 |
| Edge | V8 |
| Firefox | SpiderMonkey |
| Safari | JavaScriptCore |

Node.js is also built on V8 — which is why Node and Chrome share much of the same core language behavior.

### 1.4 What Actually Happens When JavaScript Executes

```js
const x = 10;
const y = 20;
const result = x + y;
console.log(result);
```

You write source text; the engine turns it into something executable. A simplified pipeline:

```
JavaScript source
       ↓
    Parsing
       ↓
   AST / internal representation
       ↓
 Bytecode / machine-code-oriented execution
       ↓
      Execute
```

Modern engines combine several techniques:

- **Parsing** — turning source text into a structured representation (an Abstract Syntax Tree)
- **Bytecode interpretation** — a fast, portable intermediate execution step
- **JIT compilation** — compiling hot code paths to machine code while the program runs
- **Optimization / deoptimization** — the engine speculatively optimizes based on observed types, and falls back if assumptions break
- **Garbage collection** — automatic memory reclamation

So execution is not simply "read one line, run it, read the next line" — there is a sophisticated runtime beneath the surface.

### 1.5 Interpreted or Compiled?

It's common to hear "JavaScript is an interpreted language" — this is an oversimplification. Modern engines mix interpretation and compilation. V8's general flow:

```
JavaScript
    ↓
Parse
    ↓
Generate internal representation / bytecode
    ↓
Execute
    ↓
Identify frequently executed code
    ↓
Optimize
    ↓
JIT compile optimized code
```

**JIT** stands for **Just-In-Time compilation** — the engine compiles code to machine code *while the program is running*, focusing effort on code that runs often (hot paths), which is why long-running JavaScript tends to speed up over time.

---

## 2. The Type System

### 2.1 JavaScript Is Dynamically Typed

```js
let value = 10;      // number
value = "hello";     // string
value = true;        // boolean
```

Unlike statically typed languages (`int value = 10;` in Java), JavaScript doesn't require you to declare a type ahead of time:

```js
let value = 10;
```

The type belongs to the **value**, not to the variable that holds it. The variable is just a binding that can point at values of any type over its lifetime (when declared with `let` or `var`).

### 2.2 Primitive Types Overview

JavaScript has these primitive types:

- `string`
- `number`
- `bigint`
- `boolean`
- `undefined`
- `null`
- `symbol`

### 2.3 Strings

A string represents text:

```js
const name = "Ilyass";
```

Three quoting styles exist:

```js
"Hello"
'Hello'
`Hello`
```

The backtick form is a **template literal**, which supports interpolation:

```js
const name = "Ilyass";
console.log(`Hello ${name}`);
```

```
Hello Ilyass
```

This is generally preferable to manual concatenation (`"Hello " + name`) once strings get more complex, since it avoids juggling many `+` operators.

### 2.4 Numbers

JavaScript has a single `number` type for ordinary numeric values:

```js
const age = 20;
const price = 19.99;
const temperature = -5;
```

It's based on floating-point (IEEE 754 double-precision) representation. Basic arithmetic:

```js
const a = 10;
const b = 3;

console.log(a + b); // 13
console.log(a - b); // 7
console.log(a * b); // 30
console.log(a / b); // 3.3333333333333335
console.log(a % b); // 1
```

### 2.5 The Floating-Point Problem

A famous gotcha:

```js
console.log(0.1 + 0.2);
```

You might expect `0.3`, but you get:

```
0.30000000000000004
```

This is **not a JavaScript bug** — it's a consequence of how floating-point numbers are represented in binary; certain decimal fractions can't be represented exactly. This matters a great deal in domains like money, financial calculations, and any precision-sensitive computation, where naive floating-point arithmetic can silently introduce errors.

### 2.6 BigInt

For integers beyond the safe range of `number`, JavaScript provides `BigInt`, denoted with a trailing `n`:

```js
const bigNumber = 123456789012345678901234567890n;
```

```js
const a = 12345678901234567890n;
const b = 10n;
console.log(a + b);
```

Crucially, you generally **cannot** directly mix `BigInt` and `Number`:

```js
10n + 5 // TypeError — cannot mix BigInt and other types
```

They are distinct primitive types with distinct arithmetic rules.

### 2.7 Boolean

Two possible values: `true` and `false`.

```js
const isLoggedIn = true;
const isAdmin = false;

if (isLoggedIn) {
    console.log("Welcome!");
}
```

Booleans are the backbone of conditional logic throughout the language.

### 2.8 undefined

`undefined` typically signals: **a value hasn't been assigned.**

```js
let username;
console.log(username); // undefined
```

Or, accessing a non-existent array index:

```js
const users = ["John", "Sarah"];
console.log(users[10]); // undefined
```

### 2.9 null

`null` represents an **intentional absence of value** — a deliberate "nothing here," distinct from "not yet set."

```js
let selectedUser = null; // there currently isn't a selected user
```

Conceptual distinction:

```
undefined → value hasn't been provided
null      → intentionally no value
```

### 2.10 Symbol

`Symbol` creates unique primitive values, even when given identical descriptions:

```js
const id1 = Symbol("id");
const id2 = Symbol("id");

console.log(id1 === id2); // false
```

Symbols are commonly used to create unique object property keys that won't collide with other properties, including ones added later by other code.

---

## 3. Objects & Arrays

### 3.1 Objects

Objects group related data (and behavior) together:

```js
const user = {
    name: "Ilyass",
    age: 20,
    isAdmin: false
};
```

Access properties via dot or bracket notation:

```js
console.log(user.name);
console.log(user["name"]);
```

### 3.2 Objects Can Contain Functions (Methods)

```js
const user = {
    name: "Ilyass",
    greet() {
        console.log("Hello!");
    }
};

user.greet();
```

`greet()` here is a **method** — a function that lives on an object. This is how objects combine **data + behavior**.

### 3.3 Arrays

Arrays are objects specialized for ordered collections:

```js
const numbers = [10, 20, 30, 40];
```

Indexing starts at `0`:

```js
numbers[0] // 10
numbers[1] // 20
numbers[2] // 30
```

```js
console.log(numbers.length);
```

### 3.4 Mutating Arrays

```js
const numbers = [1, 2, 3];

numbers.push(4);      // [1, 2, 3, 4] — add to end
numbers.pop();         // remove last
numbers.unshift(0);    // add to beginning
numbers.shift();       // remove first
```

---

## 4. Variables & Scope

### 4.1 var, let, const

Modern JavaScript primarily relies on `let` and `const`. `var` is legacy and should generally be avoided in new code, except when you specifically need to understand or maintain older code (its behavior is covered in depth in [Section 14](#14-hoisting--deep-dive)).

### 4.2 const

```js
const name = "Ilyass";
name = "John"; // Error — cannot reassign a const binding
```

**Important nuance:** `const` does **not** make an object immutable — it only prevents *reassignment of the variable itself*.

```js
const user = { name: "Ilyass" };
user.name = "John"; // Allowed! The variable still points to the same object.
```

The binding is locked to the same object reference; the object's internal contents can still change.

### 4.3 let

Use `let` when a variable needs to be reassigned over its lifetime:

```js
let count = 0;
count++;
count++;
// count is now 2
```

### 4.4 Scope

Scope determines *where* a variable can be accessed.

```js
if (true) {
    let x = 10;
}
console.log(x); // Error — x doesn't exist out here
```

`x` exists only inside the block it was declared in.

### 4.5 Function Scope vs Block Scope

`let` and `const` are **block-scoped**:

```js
{
    let x = 10;
}
// x is not accessible here
```

Functions also create their own scope:

```js
function test() {
    const x = 10;
    // x exists inside this function
}
```

(`var`'s scoping rules differ significantly — see [Section 14](#14-hoisting--deep-dive).)

---

## 5. Functions

### 5.1 Function Basics

Functions package reusable behavior:

```js
function greet() {
    console.log("Hello!");
}
greet();
```

### 5.2 Parameters and Arguments

```js
function greet(name) {
    console.log(`Hello ${name}`);
}
greet("Ilyass"); // Hello Ilyass
```

`name` is a **parameter** (the placeholder in the function definition); `"Ilyass"` is the **argument** (the actual value passed in at call time).

### 5.3 Return Values

```js
function add(a, b) {
    return a + b;
}

const result = add(10, 20);
console.log(result); // 30
```

Think of a function conceptually as:

```
input
  ↓
function
  ↓
output
```

### 5.4 Arrow Functions

Instead of:

```js
function add(a, b) {
    return a + b;
}
```

you can write:

```js
const add = (a, b) => {
    return a + b;
};
```

Or, since it's a single expression, even more concisely:

```js
const add = (a, b) => a + b;
```

### 5.5 Why Arrow Functions Matter

Arrow functions appear constantly in array methods, callbacks, React, promises, and event handlers:

```js
const numbers = [1, 2, 3];
const doubled = numbers.map(number => number * 2);
// [2, 4, 6]
```

Beyond brevity, arrow functions also behave differently with respect to `this` — covered in [Section 9.6](#96-arrow-functions-and-this).

---

## 6. Control Flow

### 6.1 Conditionals

```js
const age = 20;

if (age >= 18) {
    console.log("Adult");
} else {
    console.log("Minor");
}
```

JavaScript also supports `else if` chains for multiple branches.

### 6.2 Comparison Operators

```
>   <   >=   <=   ===   !==   ==   !=
```

Prefer `===` and `!==` (strict equality) over `==` and `!=` (loose equality).

### 6.3 Strict vs Loose Equality

```js
5 === 5      // true
5 === "5"    // false — different types

5 == "5"     // true — loose equality performs type coercion first
```

Strict equality (`===`) compares both value **and** type without converting either operand, which avoids a large class of subtle bugs caused by implicit coercion.

### 6.4 Logical Operators

```
&&   AND
||   OR
!    NOT
```

```js
const age = 20;
const hasLicense = true;

if (age >= 18 && hasLicense) {
    console.log("Can drive");
}
```

### 6.5 Loops

**Classic `for` loop:**

```js
for (let i = 0; i < 5; i++) {
    console.log(i);
}
// 0 1 2 3 4
```

**`while` loop:**

```js
let i = 0;
while (i < 5) {
    console.log(i);
    i++;
}
```

**`for...of`** — iterates over values of an iterable (like array elements):

```js
const users = ["John", "Sarah", "Mike"];
for (const user of users) {
    console.log(user);
}
```

**`for...in`** — iterates over an object's property *keys*:

```js
const user = { name: "Ilyass", age: 20 };
for (const key in user) {
    console.log(key);
}
// name
// age
```

Rule of thumb:

```
for...of → values / iterable elements
for...in → property keys
```

---

## 7. Array Higher-Order Methods

Modern JavaScript heavily relies on functional array methods: `map()`, `filter()`, `reduce()`, `find()`, `some()`, `every()`, `forEach()`. These are essential, especially for frontend development, because they express *what transformation you want* rather than manually writing loop bookkeeping.

### 7.1 map()

Transforms every element into a new value, producing a new array of the same length:

```js
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(number => number * 2);
// [2, 4, 6, 8]
```

Conceptually, each input maps to exactly one output:

```
1 → 2
2 → 4
3 → 6
4 → 8
```

### 7.2 filter()

Keeps only the elements that satisfy a condition, producing a (possibly shorter) new array:

```js
const numbers = [1, 2, 3, 4, 5];
const evenNumbers = numbers.filter(number => number % 2 === 0);
// [2, 4]
```

### 7.3 find()

Returns the **first** matching element (not an array):

```js
const users = [
    { id: 1, name: "John" },
    { id: 2, name: "Sarah" }
];

const user = users.find(user => user.id === 2);
// { id: 2, name: "Sarah" }
```

### 7.4 reduce()

Combines all elements into a single accumulated result:

```js
const numbers = [1, 2, 3, 4];

const sum = numbers.reduce(
    (total, number) => total + number,
    0
);
// 10
```

Step-by-step accumulation:

```
0 + 1 = 1
1 + 2 = 3
3 + 3 = 6
6 + 4 = 10
```

`reduce()` is the most general of these methods — `map()` and `filter()` can both be implemented in terms of `reduce()`, since it lets you fold a collection down into anything: a number, a string, an object, even another array.

---

## 8. Modern Syntax Features

### 8.1 Destructuring — Objects

```js
const user = { name: "Ilyass", age: 20 };
const { name, age } = user;

console.log(name);
console.log(age);
```

### 8.2 Destructuring — Arrays

```js
const numbers = [10, 20, 30];
const [a, b, c] = numbers;
// a = 10, b = 20, c = 30
```

### 8.3 Spread Operator — Arrays

The spread operator `...` expands an iterable's elements:

```js
const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4, 5];
// [1, 2, 3, 4, 5]
```

This is extremely common when working with **immutable data patterns** — instead of mutating an existing array, you build a new one that includes its old contents plus changes.

### 8.4 Spread Operator — Objects

```js
const user = { name: "Ilyass", age: 20 };

const updatedUser = {
    ...user,
    age: 21
};
// { name: "Ilyass", age: 21 }
```

This pattern (spread + override) is especially important in frameworks like React, where state updates must produce new objects rather than mutate existing ones.

### 8.5 Rest Parameter

The same `...` syntax, used in a function signature, **collects** remaining arguments into an array (the reverse of spreading):

```js
function sum(...numbers) {
    return numbers.reduce(
        (total, number) => total + number,
        0
    );
}

sum(1, 2, 3, 4); // 10
```

`...numbers` gathers all passed arguments into a real array.

### 8.6 Default Parameters

```js
function greet(name = "Guest") {
    console.log(`Hello ${name}`);
}

greet(); // Hello Guest
```

If no argument (or `undefined`) is passed, the default value is used.

### 8.7 Optional Chaining

```js
const user = {
    profile: { name: "Ilyass" }
};

console.log(user.profile?.name);
```

If `profile` doesn't exist, `user.profile?.name` short-circuits and evaluates to `undefined` instead of throwing a `TypeError` for trying to read a property off `undefined`.

### 8.8 Nullish Coalescing

The `??` operator:

```js
const username = null;
const displayName = username ?? "Guest";
// "Guest"
```

Meaning: *use the right-hand value only if the left-hand value is `null` or `undefined`* (unlike `||`, which also triggers on any falsy value like `0` or `""`).

### 8.9 Truthy and Falsy

JavaScript implicitly converts values to boolean in conditional contexts. The **falsy** values are:

```
false
0
-0
0n
""
null
undefined
NaN
```

Everything else is **truthy**, including non-empty strings, all objects, and all non-zero numbers:

```js
if ("hello") {
    console.log("Runs"); // "hello" is truthy
}
```

### 8.10 Type Coercion

JavaScript can automatically convert between types depending on the operator:

```js
console.log("5" + 2); // "52" — + with a string triggers string concatenation
console.log("5" - 2); // 3   — - forces numeric conversion
```

This dual behavior of `+` (string concatenation vs. numeric addition, depending on operand types) is one of the more surprising corners of the language, and understanding it prevents a lot of subtle bugs.

---

## 9. Objects Deep Dive / OOP

### 9.1 Objects and References

This is a critical, foundational concept.

```js
const user1 = { name: "Ilyass" };
const user2 = user1;

user2.name = "John";

console.log(user1.name); // "John"
```

Both `user1` and `user2` point to the **same underlying object** in memory — assignment of an object doesn't copy it, it copies the *reference*:

```
user1 ───────┐
             │
             ▼
          ┌───────────┐
          │  Object   │
          │ name: John│
          └───────────┘
             ▲
             │
user2 ───────┘
```

This becomes extremely important when working with things like React state and "immutable update" patterns (see [Section 8.4](#84-spread-operator--objects)) — mutating a shared object reference can produce bugs that are hard to trace, because *any* variable pointing at that object sees the change.

### 9.2 JavaScript's Object Model — Prototypes

JavaScript uses **prototype-based inheritance**. Every object has an internal link to another object (its "prototype"), from which it can inherit properties and methods.

```js
const numbers = [1, 2, 3];
numbers.map(...)
```

Where does `map()` come from? Arrays inherit their built-in methods from `Array.prototype` — you never defined `map` yourself, but every array automatically has access to it via this prototype chain.

### 9.3 Classes

JavaScript provides `class` syntax for a more familiar, structured way to create objects with shared behavior:

```js
class User {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        console.log(`Hello ${this.name}`);
    }
}

const user = new User("Ilyass", 20);
user.greet();
```

### 9.4 Classes Are Built on Prototypes

This distinction matters: `class` does **not** turn JavaScript into a fundamentally class-based language the way Java is. Underneath, `class` is syntax layered over the same prototype-based inheritance system described in 9.2. Methods defined in a class body end up on the class's prototype object, and instances inherit from it — the mechanism is the same, just with cleaner syntax on top.

### 9.5 this

`this` is one of the most commonly misunderstood JavaScript concepts.

```js
const user = {
    name: "Ilyass",
    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Here `this` refers to **the object used to call the method** — calling `user.greet()` makes `this` refer to `user`. But critically, `this` is determined by *how* a function is called, not by where it's defined — the same function can behave differently depending on the call site.

### 9.6 Arrow Functions and this

Arrow functions do **not** create their own `this` binding — they inherit `this` from their surrounding (lexical) scope:

```js
const user = {
    name: "Ilyass",
    greet: () => {
        console.log(this.name);
    }
};
```

This generally will **not** behave like a normal method, because the arrow function's `this` doesn't refer to `user` — it refers to whatever `this` was in the scope where the arrow function was defined (often the global scope). Therefore, when you need a method's dynamic, call-site-dependent `this`, ordinary `function` syntax (as in 9.5) is usually the appropriate choice, and arrow functions are better suited to callbacks where you *want* to inherit the surrounding `this`.

---

## 10. Closures & Execution Model

### 10.1 Closures

Closures are one of the most important advanced JavaScript concepts.

```js
function createCounter() {
    let count = 0;

    return function () {
        count++;
        return count;
    };
}

const counter = createCounter();

console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

How can the returned inner function still access `count` after `createCounter()` has already finished executing? Because the inner function **closes over** the variables from its surrounding lexical environment — it keeps a live reference to that environment, not just a snapshot of the value.

```
createCounter()
      │
      ▼
 ┌───────────────┐
 │ count = 0     │
 │               │
 │ returned fn ──┼──────┐
 └───────────────┘      │
                        │
                        ▼
                   still accesses
                   count
```

Closures underpin an enormous number of JavaScript patterns: callbacks, event handlers, React hooks, private/encapsulated state, functional programming utilities, and asynchronous code generally.

### 10.2 Execution Context

To understand JavaScript deeply — including hoisting (Section 14) and closures above — you need to understand **execution contexts**: the environment JavaScript creates in order to execute a piece of code.

The main conceptual types:

- **Global Execution Context** — created once, when your program starts
- **Function Execution Context** — created fresh every time a function is called

```js
const x = 10;

function test() {
    const y = 20;
}
```

Initially there is only the Global Execution Context. When `test()` runs, a new Function Execution Context is created for that call:

```
Global Execution Context
        │
        ▼
Function Execution Context
```

Each function *call* — not each function definition — gets its own execution environment, which is why recursive calls each get their own independent set of local variables.

### 10.3 Call Stack

JavaScript tracks function execution using a **call stack** — a last-in-first-out structure of "which function is currently running, and who called it."

```js
function first() {
    second();
}
function second() {
    third();
}
function third() {
    console.log("Hello");
}

first();
```

The stack grows as each function calls the next:

```
third()
second()
first()
global
```

And unwinds as each function returns, from the top down:

```
second()          first()            global
first()    →      global      →      (empty)
global
```

This is exactly why deep, unterminated recursion produces the classic error:

```
Maximum call stack size exceeded
```

— the stack has a finite size, and each nested call adds another frame without any returning.

---

## 11. Browser Runtime & DOM

### 11.1 The Browser Runtime

JavaScript the *language* is only part of the picture in a browser — the browser layers additional capabilities on top:

```
              Browser
                 │
      ┌──────────┼──────────┐
      │          │          │
 JavaScript     DOM       Web APIs
    Engine                  │
      │                     │
      └──────────┬──────────┘
                 │
            Event Loop
```

Browser-provided APIs include things like `setTimeout()`, `fetch()`, `localStorage`, `WebSocket`, DOM APIs, and Geolocation — none of these are part of the core ECMAScript language specification; they are provided by the host environment.

### 11.2 The DOM

**DOM** stands for **Document Object Model** — a tree-like, in-memory representation of your HTML that JavaScript can read and modify.

```html
<h1>Hello</h1>
<button>Click</button>
```

The browser parses this into a tree:

```
Document
│
├── h1
│    └── "Hello"
│
└── button
     └── "Click"
```

### 11.3 Selecting DOM Elements

```html
<h1 id="title">Hello</h1>
```

```js
const title = document.getElementById("title");
// or
const title = document.querySelector("#title");
```

### 11.4 Changing the DOM

```js
const title = document.querySelector("#title");
title.textContent = "Hello Ilyass";
```

The page updates immediately. You can also modify `classList`, attributes, inline styles, and even the element's children — the DOM is a live, mutable structure.

### 11.5 Events

Web applications are fundamentally **event-driven**.

```html
<button id="button">Click me</button>
```

```js
const button = document.querySelector("#button");

button.addEventListener("click", () => {
    console.log("Button clicked!");
});
```

The flow:

```
User clicks button
        ↓
Browser detects event
        ↓
Event is dispatched
        ↓
Your event listener executes
```

### 11.6 A Complete DOM Example

```html
<!DOCTYPE html>
<html>
<body>

<h1 id="counter">0</h1>
<button id="increment">Increment</button>

<script>
    const counter = document.querySelector("#counter");
    const button = document.querySelector("#increment");

    let count = 0;

    button.addEventListener("click", () => {
        count++;
        counter.textContent = count;
    });
</script>

</body>
</html>
```

Walking through it step by step:

1. The browser builds the DOM from the HTML.
2. JavaScript selects the `counter` and `button` elements.
3. `let count = 0;` initializes local state.
4. `button.addEventListener(...)` registers a click handler.
5. The user clicks the button.
6. The browser invokes the registered callback.
7. `count++` increments the counter.
8. `counter.textContent = count;` writes the new value back into the DOM, which the browser re-renders.

---

## 12. Asynchronous JavaScript — Introduction

This section introduces the core ideas; [Section 15](#15-asynchronous-javascript--deep-dive) covers the same territory with much greater depth and precision.

### 12.1 The Core Idea

JavaScript execution is fundamentally single-threaded in the common browser model, yet browsers provide **asynchronous APIs** so long-running operations don't freeze everything.

```js
setTimeout(() => {
    console.log("Finished");
}, 2000);

console.log("Hello");
```

```
Hello
Finished
```

JavaScript doesn't block for two seconds — it schedules the callback and moves on immediately.

### 12.2 Event Loop (Brief)

The **event loop** is the mechanism that coordinates asynchronous callbacks with the JavaScript call stack, continuously checking whether there's queued work that's ready to run once the call stack is empty.

### 12.3 Promises (Basics)

A **Promise** represents the eventual result of an asynchronous operation. Conceptual states:

```
pending
   │
   ├── fulfilled
   │
   └── rejected
```

```js
const promise = new Promise((resolve, reject) => {
    resolve("Success");
});

promise.then(result => {
    console.log(result);
});
```

### 12.4 A Promise-Returning Example

```js
fetch("/api/users")
```

`fetch()` returns a Promise:

```
fetch()
  │
  ▼
Promise
  │
  ├── waiting
  │
  ├── success
  │
  └── failure
```

### 12.5 async/await (Basics)

```js
async function getUsers() {
    const response = await fetch("/api/users");
    const users = await response.json();
    console.log(users);
}
```

`async`/`await` lets asynchronous code *read* like synchronous code, while still behaving asynchronously underneath.

### 12.6 What await Actually Does

```js
const response = await fetch("/api/users");
```

This does **not** freeze the entire JavaScript runtime until the request completes. Instead, the async function itself pauses at that point, letting other work run in the meantime; when the awaited Promise settles, execution resumes where it left off.

### 12.7 Error Handling

```js
async function getUsers() {
    try {
        const response = await fetch("/api/users");
        const users = await response.json();
        console.log(users);
    } catch (error) {
        console.error(error);
    }
}
```

### 12.8 HTTP Requests with fetch

```js
const response = await fetch("https://example.com/api/users");
const data = await response.json();
```

A POST request:

```js
await fetch("/api/users", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        name: "Ilyass",
        age: 20
    })
});
```

### 12.9 JSON

JSON is the standard format for frontend/backend communication:

```js
const json = JSON.stringify({ name: "Ilyass", age: 20 }); // object → JSON string
const object = JSON.parse(json);                          // JSON string → object
```

---

## 13. Modules & Ecosystem

### 13.1 Modules

Modern JavaScript supports splitting code across files using **modules**.

```
project/
├── main.js
├── math.js
```

`math.js`:

```js
export function add(a, b) {
    return a + b;
}
```

`main.js`:

```js
import { add } from "./math.js";
console.log(add(10, 20));
```

### 13.2 Default Exports

```js
export default function add(a, b) {
    return a + b;
}
```

```js
import add from "./math.js";
```

Note the syntax difference: a **named export** (`export function add`) must be imported with matching curly braces (`import { add }`); a **default export** can be imported under any name without braces.

### 13.3 Why Modules Matter

Without modules, a large application risks collapsing into one enormous, tangled file:

```
script.js
    ↓
10,000 lines
    ↓
everything mixed together
```

Modules let you organize code by responsibility:

```
auth.js
user.js
api.js
utils.js
movies.js
components/
```

Each module can have a single, focused responsibility — this becomes especially important as applications grow, and is foundational to how React and Node applications are structured.

### 13.4 npm and the JavaScript Ecosystem

JavaScript has a massive package ecosystem. **npm** is the standard tool for installing packages:

```bash
npm install axios
```

This adds a dependency to your project, typically producing:

```
node_modules/
package.json
package-lock.json
```

### 13.5 package.json

```json
{
    "name": "my-app",
    "version": "1.0.0",
    "scripts": {
        "dev": "vite"
    },
    "dependencies": {
        "react": "^19.0.0"
    }
}
```

This file describes project metadata, dependencies, and scripts (commands you can run, like `npm run dev`).

### 13.6 Node.js

Node.js lets JavaScript run **outside** the browser.

```js
console.log("Hello from Node");
```

```bash
node app.js
```

Node exposes APIs for things browsers don't provide directly: filesystem access, networking, HTTP servers, OS processes, streams, and environment variables.

### 13.7 Browser JavaScript vs Node.js

This distinction matters a great deal in practice:

**Browser JavaScript** provides: `document.querySelector(...)`, `window`, `document`, `localStorage`, `fetch`

**Node.js** provides: `import fs from "fs"`, `process`, `Buffer`, HTTP modules, streams

Some APIs (like `fetch` in modern Node) overlap, but fundamentally these are two different host environments layered on top of the same core JavaScript language — code written assuming DOM APIs won't run in Node without modification, and vice versa.

---

## 14. Hoisting — Deep Dive

### 14.1 A Motivating Example

```js
console.log(x);
var x = 10;
```

Beginners often expect a `ReferenceError`. JavaScript instead produces:

```
undefined
```

Conceptually, the engine treats this somewhat like two separate steps:

```js
var x;        // declaration
console.log(x);
x = 10;        // assignment
```

The key insight: **the declaration is available before the line where it textually appears, but the assignment is not.**

### 14.2 What "Hoisting" Actually Means

It's tempting to imagine JavaScript literally rearranging your source code, physically moving declarations to the top of the file. **That is not what actually happens.** Instead, when JavaScript begins executing a scope, it first does setup work associated with creating an *execution context* (introduced in [10.2](#102-execution-context)). During this setup, declarations are registered with the appropriate environment *before* any statements run. Only then does the code execute normally, top to bottom.

So:

```js
console.log(name);
var name = "Ilyass";
```

is better understood as an ordered sequence of engine operations:

```
1. Create a binding called "name"
2. Initialize it according to the rules for `var`
3. Start executing statements
4. console.log(name)
5. Assign "Ilyass" to name
```

Result: `undefined`.

### 14.3 Execution Contexts (Recap for Hoisting)

- **Global execution context** — created when the program starts (`const x = 10;` at the top level runs here)
- **Function execution context** — created for each function *call*
- **Eval execution context** — created for code run via `eval()` (generally avoided in practice)

### 14.4 The Two Phases

A very useful mental model: every execution context goes through a **creation/setup phase** and then an **execution phase**.

```
Execution Context
        │
        ├── Creation / setup
        │
        └── Execution
```

During creation, JavaScript determines what bindings the code needs. Only then do the actual statements run.

```js
console.log(x);
var x = 10;
```

```
CREATION PHASE
--------------
Create x
Initialize x → undefined


EXECUTION PHASE
---------------
console.log(x)
        ↓
undefined

x = 10
```

This two-phase model is the foundation of everything else in this section.

### 14.5 var Hoisting

```js
console.log(x);
var x = 10;
```

```
undefined
```

The `var` declaration is created **and initialized to `undefined`** during the setup phase — before execution ever reaches the `console.log`.

### 14.6 Declaration vs Initialization

This distinction is essential.

```js
var x = 100;
```

Two things are happening:

```js
var x;      // Declaration
x = 100;    // Initialization / assignment
```

During setup: `x → undefined`. Only later, during execution, does `x → 100`. Therefore:

```js
console.log(x);
var x = 100;
```

produces `undefined`, **not** `100`.

### 14.7 Multiple Declarations Example

```js
console.log(a);
console.log(b);

var a = 10;
var b = 20;
```

Creation phase: `a → undefined`, `b → undefined`. Then execution:

```
undefined
undefined
```

Only after both `console.log` calls do the assignments `a = 10` and `b = 20` happen.

### 14.8 var Inside a Function

Hoisting applies inside functions too:

```js
function test() {
    console.log(x);
    var x = 10;
}

test();
```

```
undefined
```

When `test()` is called, JavaScript creates a Function Execution Context for it, and the same two-phase process applies within that context: `x → undefined` during setup, then `console.log(x)` runs before `x = 10`.

### 14.9 var Is Function-Scoped

```js
function test() {
    if (true) {
        var x = 10;
    }
    console.log(x);
}

test();
```

```
10
```

Because **`var` does not have block scope.** The `if` block does not create a separate `var` scope — `x` belongs to the entire enclosing function:

```
test()
│
├── x
│
└── if block
```

So `console.log(x)`, outside the `if` block but still inside `test()`, can access it.

### 14.10 let Is Also Hoisted — But Differently

A common but misleading claim is "`let` is not hoisted." A more accurate framing: **`let` bindings are created before execution reaches the declaration** (so in that sense they are hoisted), **but they are not initialized to `undefined`** the way `var` is.

```js
console.log(x);
let x = 10;
```

```
ReferenceError
```

The binding for `x` exists, but it isn't initialized yet — this period is called the **Temporal Dead Zone (TDZ)**.

### 14.11 The Temporal Dead Zone

```js
console.log(x);
let x = 10;
```

Creation phase: `x → uninitialized`. Then, when execution reaches `console.log(x)`, JavaScript finds that `x` exists as a binding but is uninitialized — accessing it in this state throws a `ReferenceError`, rather than silently returning `undefined`.

```
                    TDZ
                     │
                     ▼
let x = 10;
│           │
│           └── x becomes initialized
│
└── x binding created
```

The TDZ is the span between when a lexical binding is created and when it is actually initialized by its declaration statement.

### 14.12 Why "Temporal"?

Because it's about *timing during execution*, not about position in the source file.

```js
let x;
console.log(x); // undefined — no TDZ issue, because the declaration already ran
```

vs.

```js
console.log(x);
let x; // ReferenceError — accessed before the declaration executed
```

The distinguishing factor is *when*, during execution, the variable is accessed relative to when its declaration statement runs.

### 14.13 const Behaves Similarly

```js
console.log(x);
const x = 10;
```

```
ReferenceError
```

`const` also creates its binding ahead of time, but that binding stays uninitialized until the declaration is actually evaluated:

```
Creation:
x → uninitialized

Execution:
x → 10
```

### 14.14 let vs const vs var — Comparison Table

| Feature | var | let | const |
|---|---|---|---|
| Binding created before declaration | Yes | Yes | Yes |
| Initialized during setup | `undefined` | No | No |
| Has a TDZ | No | Yes | Yes |
| Accessing before declaration | Yes, gets `undefined` | No | No |
| Function scoped | Yes | No | No |
| Block scoped | No | Yes | Yes |
| Can redeclare in same scope | Yes | No | No |
| Must initialize immediately | No | No | Yes |

For modern JavaScript, `let` and `const` are generally preferred over `var`, mainly because block scoping and the TDZ catch a large class of bugs (like accidental use-before-declaration or leaking loop variables) that `var` allows silently.

### 14.15 What Exactly Happens With let — Step by Step

```js
console.log(x);
let x = 10;
```

1. **Create the binding:** `x → uninitialized`.
2. **Start executing:** reach `console.log(x)`. JavaScript asks "what is `x`?" — the binding exists, but is `uninitialized`.
3. Because of that, JavaScript throws `ReferenceError` — execution never reaches `x = 10`.

### 14.16 Function Declarations Are Heavily Hoisted

```js
sayHello();

function sayHello() {
    console.log("Hello");
}
```

This **works**, printing `Hello`. Function declarations are made fully available — not just their name, but the entire function body — before their position in the source code, during the setup phase. Conceptually:

```
// Function is already available
sayHello();
```

### 14.17 Function Declarations vs Function Expressions

This distinction is crucial and frequently tested in interviews.

**Function declaration** (works when called early):

```js
sayHello();

function sayHello() {
    console.log("Hello");
}
```

**Function expression with `var`** (fails):

```js
sayHello();

var sayHello = function () {
    console.log("Hello");
};
```

```
TypeError: sayHello is not a function
```

Why? `var sayHello;` is initialized to `undefined` during setup. When execution reaches `sayHello();`, it's effectively calling `undefined()`, which throws a `TypeError`. Only later does `sayHello = function () {...}` actually assign the function.

### 14.18 Function Expression With let

```js
sayHello();

let sayHello = function () {
    console.log("Hello");
};
```

```
ReferenceError
```

`sayHello` is in the Temporal Dead Zone at the point of the call — accessing an uninitialized `let` binding throws `ReferenceError`, not `TypeError`.

### 14.19 Function Declaration vs Expression With const

```js
add(2, 3);

const add = function(a, b) {
    return a + b;
};
```

```
ReferenceError
```

Here `add` follows `const`'s rules — it isn't callable before its own initialization runs.

Compare to an actual function declaration, which is available immediately, regardless of `const`/`let`/`var` semantics — because function declarations are a distinct construct with their own hoisting rule.

### 14.20 Arrow Functions

```js
hello();

const hello = () => {
    console.log("Hello");
};
```

```
ReferenceError
```

The important factor here isn't that it's an arrow function specifically — it's that `hello` is declared with `const`, so the same TDZ rule applies as with any other `const`/`let` binding holding a function.

### 14.21 var + Function Expression

```js
hello();

var hello = () => {
    console.log("Hello");
};
```

Conceptually:

```js
var hello;
hello();     // hello is undefined here
hello = () => {
    console.log("Hello");
};
```

At the moment of the call, `hello → undefined`, so the call becomes `undefined()`:

```
TypeError
```

### 14.22 A Very Important Interview Distinction — Six Cases Compared

```js
// Case 1
console.log(x);
var x = 10;
// Result: undefined

// Case 2
console.log(x);
let x = 10;
// Result: ReferenceError

// Case 3
console.log(x);
const x = 10;
// Result: ReferenceError

// Case 4
hello();
function hello() {
    console.log("Hello");
}
// Result: Works fine

// Case 5
hello();
var hello = function() {};
// Result: TypeError

// Case 6
hello();
let hello = function() {};
// Result: ReferenceError
```

### 14.23 Why Does the Error Differ Between var and let/const?

This comparison is the clearest way to internalize hoisting.

For `var x;` — during setup, `x → undefined`. So calling `x()` becomes `undefined()`, producing `TypeError` (you tried to *call a non-function value*).

For `let x;` — before initialization, `x → uninitialized` (in the TDZ). Trying to access it at all, in any way, produces `ReferenceError` (you tried to *access a binding that doesn't yet exist in a usable state*).

```
var
 ↓
undefined
 ↓
using it can produce TypeError depending on operation


let / const
 ↓
uninitialized
 ↓
ReferenceError
```

### 14.24 Block Scope and Hoisting

```js
{
    console.log(x);
    let x = 10;
}
```

```
ReferenceError
```

The block itself creates a lexical scope, and `x`'s TDZ applies within that block:

```
Global scope
│
└── Block scope
      │
      └── x → uninitialized
```

### 14.25 A Surprising Shadowing Example

```js
let x = 100;

{
    console.log(x);
    let x = 200;
}
```

You might expect `100` (falling back to the outer `x`), but you get:

```
ReferenceError
```

Why? The inner `let x = 200;` creates a **new binding** local to the block. Inside that block, any reference to `x` refers to *that inner variable*, not the outer one — and that inner variable is in its own TDZ at the point of the `console.log`. The lookup doesn't "skip past" the inner declaration to find the outer one.

```
Global scope
    x → 100

Block scope
    x → uninitialized
```

### 14.26 Shadowing Explained

The inner variable **shadows** the outer one — for the entire block, the name `x` is bound to the inner declaration, hiding the outer one from that point of view.

```js
let x = 100;

{
    let x = 200;
    console.log(x); // 200
}

console.log(x); // 100
```

Two entirely separate variables exist, one in each scope.

### 14.27 var and Block Scope

```js
var x = 100;

{
    var x = 200;
}

console.log(x);
```

```
200
```

Because `var` doesn't create block scope — both declarations refer to the **same** function/global-level `var` binding, so the inner assignment overwrites the outer one directly (no shadowing occurs, because there's no separate scope for `var` at the block level).

### 14.28 Hoisting Inside Loops

With `var`:

```js
for (var i = 0; i < 3; i++) {
    console.log(i);
}
console.log(i);
```

```
0
1
2
3
```

`i` leaks out of the loop because `var` isn't block-scoped — `i` belongs to the enclosing function/global scope.

With `let`:

```js
for (let i = 0; i < 3; i++) {
    console.log(i);
}
console.log(i);
```

The final `console.log(i)` produces `ReferenceError`, because `i` belongs strictly to the loop's own block scope and doesn't exist outside it. (This is also why `let` in loops is generally safer when closures capture the loop variable across iterations — each iteration effectively gets its own `i`.)

### 14.29 Hoisting and Function Parameters

Parameters are bindings too:

```js
function test(x) {
    console.log(x);
}

test(10);
```

When the function executes, `x → 10` immediately — the parameter binding already exists as part of the environment created for that function call, before the body's first statement runs. Parameters aren't usually described using the word "hoisting" the way `var` is, but conceptually they're part of the same setup-phase machinery.

### 14.30 A Tricky Parameter Example

```js
function test(x) {
    console.log(x);
    var x = 20;
    console.log(x);
}

test(10);
```

```
10
20
```

Why? The parameter `x` already establishes the binding with value `10` when the function begins executing. The `var x` declaration inside the body doesn't create a *separate* variable in the same function scope — it refers to the same `x`. So the first `console.log` sees the parameter's value (`10`), and only the later `var x = 20;` assignment changes it to `20`.

---

## 15. Asynchronous JavaScript — Deep Dive

### 15.1 What Does "Asynchronous" Mean?

JavaScript is often described as **single-threaded but asynchronous** — these ideas sound contradictory but aren't.

**Single-threaded:** JavaScript normally executes your code using one main execution thread — it doesn't run two pieces of your JavaScript simultaneously on that thread.

```js
console.log("A");
console.log("B");
console.log("C");
```

```
A
B
C
```

Each statement finishes completely before the next one begins.

### 15.2 So How Can JavaScript Be Asynchronous?

```js
console.log("A");

setTimeout(() => {
    console.log("B");
}, 3000);

console.log("C");
```

You might expect JavaScript to pause for 3 seconds before continuing. Instead, the actual output is:

```
A
C
B
```

JavaScript doesn't sit there waiting. Instead it effectively says: *"Start this timer. I'll continue executing my code. When the timer is ready, arrange for this callback to run."* This is the foundational idea of asynchronous programming.

### 15.3 Why We Need Asynchronous Programming

Imagine JavaScript had to wait synchronously for every operation, including network requests, which can take anywhere from a few milliseconds to many seconds:

```
User clicks button
        ↓
Network request
        ↓
JavaScript waits
        ↓
UI can't respond
        ↓
Page feels frozen
```

Asynchronous programming avoids this by letting JavaScript start an operation, continue doing other work, and handle the result only once it's ready:

```
Start network request
       ↓
Continue doing other work
       ↓
Network finishes
       ↓
Handle the result
```

### 15.4 The Important Components

```
JavaScript Engine
       │
       ├── Call Stack
       │
       └── Heap
       
Browser / Node.js runtime
       │
       ├── Web APIs / Runtime APIs
       │
       ├── Task queues
       │
       └── Event Loop
```

Key concepts to understand together: call stack, Web APIs/runtime APIs, callbacks, task queues, the microtask queue, the event loop, Promises, and `async`/`await`.

### 15.5 The Call Stack (Recap, Traced Through Async Code)

```js
function one() {
    two();
}
function two() {
    three();
}
function three() {
    console.log("Hello");
}

one();
```

```
CALL STACK (empty)
        ↓ one();
CALL STACK: one()
        ↓ two();
CALL STACK: two() → one()
        ↓ three();
CALL STACK: three() → two() → one()
        ↓ console.log("Hello") runs, three() returns
CALL STACK: two() → one()
        ↓ two() returns
CALL STACK: one()
        ↓ one() returns
CALL STACK (empty)
```

The call stack manages purely synchronous execution.

### 15.6 What Happens With Asynchronous Operations?

```js
console.log("A");

setTimeout(() => {
    console.log("B");
}, 2000);

console.log("C");
```

**Step 1:** `console.log("A")` runs, prints `A`, call stack empties.

**Step 2:** JavaScript reaches `setTimeout(...)`. Importantly, `setTimeout` itself isn't a core language feature — it's provided by the runtime (browser or Node):

```
JavaScript
    │
    │ setTimeout(...)
    ↓
Runtime timer system
```

The callback is registered to run later; JavaScript does not wait for it.

**Step 3:** JavaScript immediately continues to `console.log("C")`, printing `C`. So far: `A`, `C`. Then the main synchronous execution finishes.

**Step 4:** After roughly 2 seconds, the runtime determines the timer is ready — but the callback does **not** necessarily run immediately. It has to wait until JavaScript is free to execute it:

```
Timer finishes
      ↓
Callback becomes eligible
      ↓
Callback goes into task queue
      ↓
Event loop checks
      ↓
Call stack is empty
      ↓
Callback pushed onto call stack
      ↓
Callback executes
```

Then `B` is printed.

### 15.7 The Event Loop

The event loop is what continuously coordinates queued asynchronous work with the JavaScript call stack:

```
                 ┌──────────────────┐
                 │   Call Stack     │
                 └────────┬─────────┘
                          │
                          │
                    Event Loop
                          │
              ┌───────────┴───────────┐
              ↓                       ↓
        Microtask Queue          Task Queue
        Promise callbacks        Timers/events
```

The event loop repeatedly checks: is the call stack empty? If so, is there queued work ready to run?

### 15.8 JavaScript Doesn't Run Things in Parallel by Magic

```js
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

```
A
C
B
```

Even with a delay of `0`, the callback still doesn't run immediately. `setTimeout(..., 0)` effectively means: *"Don't run this callback before 0 milliseconds have elapsed; once it's eligible, place it in the task queue."* It does not mean "run this synchronously, right now."

### 15.9 Callbacks

A **callback** is a function passed into another function so it can be invoked later.

```js
function greet(name, callback) {
    console.log("Hello " + name);
    callback();
}

greet("Ilyass", () => {
    console.log("Finished");
});
```

Callbacks become especially useful for asynchronous work:

```js
setTimeout(() => {
    console.log("Finished");
}, 2000);
```

The callback tells the runtime: *"When this asynchronous operation is ready, execute this function."*

### 15.10 The Callback Problem ("Callback Hell")

With several dependent asynchronous steps, code can become deeply nested:

```js
getUser(userId, user => {
    getMovies(user.id, movies => {
        getRecommendations(movies, recommendations => {
            saveRecommendations(recommendations, result => {
                console.log(result);
            });
        });
    });
});
```

This nesting pattern is commonly called **callback hell**. The problem isn't that callbacks are inherently bad — it's that managing complex, sequential asynchronous control flow purely with nested callbacks becomes hard to read and maintain. Promises (next) were introduced specifically to address this.

### 15.11 Promises

A **Promise** is an object representing the eventual result of an asynchronous operation — think of it as: *"I don't have the result yet, but I will eventually tell you whether the operation succeeded or failed."*

Three states:

```
             ┌─────────────┐
             │   PENDING   │
             └──────┬──────┘
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
   ┌──────────────┐    ┌──────────────┐
   │  FULFILLED   │    │   REJECTED   │
   └──────────────┘    └──────────────┘
```

A Promise starts `pending` and eventually settles into either `fulfilled` or `rejected` — and once settled, it never changes state again.

### 15.12 Creating a Promise

```js
const promise = new Promise((resolve, reject) => {
    // asynchronous work
    resolve("Success");
});
```

The executor function receives two functions: `resolve` (fulfills the Promise) and `reject` (rejects it with a reason/error).

### 15.13 A Timed Promise Example

```js
const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("Data received");
    }, 2000);
});
```

Initially `pending`; after 2 seconds, `fulfilled` with the value `"Data received"`.

### 15.14 Consuming a Promise With .then()

```js
promise.then(result => {
    console.log(result);
});
```

The `.then()` callback runs once the Promise is fulfilled:

```js
const promise = new Promise(resolve => {
    setTimeout(() => {
        resolve("Hello");
    }, 2000);
});

promise.then(result => {
    console.log(result);
});
// After the timer: "Hello"
```

### 15.15 .catch()

`.catch()` handles rejection:

```js
const promise = new Promise((resolve, reject) => {
    reject("Something went wrong");
});

promise.catch(error => {
    console.log(error);
});
// "Something went wrong"
```

### 15.16 .finally()

Runs regardless of success or failure — useful for cleanup logic:

```js
promise
    .then(result => {
        console.log(result);
    })
    .catch(error => {
        console.log(error);
    })
    .finally(() => {
        console.log("Finished");
    });
```

### 15.17 The Really Important Part: Promise Callbacks Are Microtasks

```js
console.log("A");

Promise.resolve().then(() => {
    console.log("B");
});

console.log("C");
```

```
A
C
B
```

Even though `Promise.resolve()` is *already* fulfilled, `.then()`'s callback still doesn't run immediately.

### 15.18 Promise Execution, Step by Step

1. `console.log("A")` runs → prints `A`.
2. `Promise.resolve().then(() => console.log("B"))` — the Promise is already fulfilled, but the callback is not invoked synchronously; it is scheduled as a **microtask**:

```
Microtask Queue
[ print B ]
```

3. Execution continues to `console.log("C")` → prints `C`.
4. Synchronous code is now finished. The event loop processes pending microtasks:

```
Microtask Queue
[ print B ]
        ↓
    executes
        ↓
      "B"
```

Final result: `A`, `C`, `B`.

### 15.19 Microtasks vs Tasks

Two different categories of scheduled asynchronous work:

**Task queue** examples: `setTimeout`, `setInterval`, certain browser events.

**Microtask queue** examples: `Promise.then()`, `Promise.catch()`, `Promise.finally()`, `queueMicrotask()`.

**Microtasks have priority** — after a JavaScript "task" finishes and the call stack empties, the runtime processes **all** pending microtasks before moving on to the next task.

### 15.20 Promise vs setTimeout — Combined Example

```js
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

Promise.resolve().then(() => {
    console.log("C");
});

console.log("D");
```

Trace:

1. `A` prints.
2. `setTimeout(...)` schedules its callback into the **task queue**.
3. `Promise.resolve().then(...)` schedules its callback into the **microtask queue**.
4. `D` prints. Synchronous execution finishes.
5. Microtasks run first: `C` prints.
6. Only then does the task queue get processed: `B` prints.

```
A
D
C
B
```

### 15.21 A Useful Mental Model of the Event Loop

```
1. Execute synchronous JavaScript
             ↓
2. Call stack becomes empty
             ↓
3. Process microtasks
             ↓
4. Perform rendering / runtime work as appropriate
             ↓
5. Process another task
             ↓
6. Process microtasks again
             ↓
7. Repeat
```

The actual browser event-loop algorithm has more nuance, but this model is accurate enough to reason correctly about ordering in nearly all real code.

### 15.22 async Functions Always Return a Promise

```js
async function getData() {
    return "Hello";
}
```

Even though the code says `return "Hello";`, calling `getData()` does **not** give you `"Hello"` directly — it gives you a Promise:

```js
const result = getData();
console.log(result); // Promise { "Hello" }
```

Conceptually, this `async function` behaves like:

```js
function getData() {
    return Promise.resolve("Hello");
}
```

### 15.23 Why Does async Return a Promise?

Because `async` functions are specifically designed to represent asynchronous control flow, and Promises are the standard way to represent "a value that will exist eventually."

```js
async function getUser() {
    const response = await fetch("/api/user");
    return response;
}

getUser().then(user => {
    console.log(user);
});
```

### 15.24 await

`await` means, roughly: *"wait for this Promise to settle before continuing this async function."* Critically, it does **not** mean *"block the entire JavaScript thread."* It only suspends the execution of the specific `async` function it appears in.

```js
async function getUser() {
    const response = await fetch("/api/user");
    console.log(response);
}
```

### 15.25 Why This Distinction Is Critical

```js
async function test() {
    console.log("A");
    await somePromise;
    console.log("B");
}

console.log("C");
test();
console.log("D");
```

Assuming `somePromise` is still pending at this point, the output is:

```
C
A
D
B
```

### 15.26 Step-by-Step await Trace

1. `console.log("C")` → prints `C`.
2. `test()` is called; the function body begins.
3. `console.log("A")` inside `test()` → prints `A`.
4. `await somePromise;` — the Promise hasn't settled yet, so `test()` is **suspended** at this point. Crucially, JavaScript itself is *not* frozen — control returns to the caller.
5. `console.log("D")` (outside `test()`) → prints `D`.
6. Eventually `somePromise` settles. The continuation of `test()` after the `await` is scheduled to resume.
7. `console.log("B")` runs → prints `B`.

Final: `C`, `A`, `D`, `B`.

### 15.27 await as Promise-Based Continuation

```js
async function test() {
    const value = await promise;
    console.log(value);
}
```

is conceptually similar to:

```js
function test() {
    return promise.then(value => {
        console.log(value);
    });
}
```

This isn't a literal, mechanical source-code transformation in every detail, but it's an excellent mental model: `await` lets you write Promise-based, continuation-style asynchronous code using syntax that *looks* sequential.

### 15.28 A Multi-Stage await Example

```js
async function getUser() {
    const response = await fetch("/api/user");
    const user = await response.json();
    return user;
}
```

There are actually two separate asynchronous stages here — the network request itself, and then parsing the response body as JSON, each of which is its own Promise.

```
getUser()
   │
   ↓
fetch("/api/user")
   │
   ↓
Promise pending
   │
   │ network request happens
   │
   ↓
Promise fulfilled
   │
   ↓
resume getUser()
   │
   ↓
response.json()
   │
   ↓
Promise pending
   │
   ↓
Promise fulfilled
   │
   ↓
resume getUser()
   │
   ↓
return user
```

### 15.29 What Happens to the async Function During await?

```js
async function example() {
    console.log("A");
    await promise;
    console.log("B");
}
```

When JavaScript reaches `await promise;`, it doesn't keep that function's execution frame actively running and blocking. Instead, the function's continuation is conceptually "remembered" — *"once the Promise resolves, continue with `console.log('B')`"* — and the function yields control back to whatever called it, or to the surrounding synchronous code. This is precisely why other JavaScript can keep running in the meantime.

### 15.30 Proof That await Doesn't Block Everything

```js
async function first() {
    console.log("first start");

    await new Promise(resolve => {
        setTimeout(resolve, 2000);
    });

    console.log("first end");
}

async function second() {
    console.log("second");
}

first();
second();
```

Execution:

```
first start
second
```

Then, roughly 2 seconds later:

```
first end
```

The `await` inside `first()` did **not** prevent `second()` from running while `first()` was suspended waiting on its timer.

### 15.31 await and Microtasks

```js
async function test() {
    console.log("A");
    await Promise.resolve();
    console.log("B");
}

console.log("C");
test();
console.log("D");
```

Trace:

1. `console.log("C")` → `C`.
2. `test()` is called; it prints `A` immediately. So far: `C`, `A`.
3. Execution reaches `await Promise.resolve();`. The Promise is already fulfilled, but even so, the continuation after `await` still runs asynchronously — it's scheduled as a microtask:

```
Microtask Queue
[ continue test() → console.log("B") ]
```

4. Execution continues outside the function: `console.log("D")` → `D`.
5. Synchronous execution is complete. The microtask runs: `console.log("B")` → `B`.

Final: `C`, `A`, `D`, `B`.

### 15.32 Error Handling With async/await

One of the biggest advantages of `async`/`await` is being able to use ordinary-looking `try`/`catch`:

```js
async function getUser() {
    try {
        const response = await fetch("/api/user");
        const user = await response.json();
        return user;
    } catch (error) {
        console.error(error);
    }
}
```

If **either** `fetch(...)` or `response.json()` rejects, the thrown error is caught by the single `catch` block.

### 15.33 The Equivalent Promise-Chain Version

Without `async`/`await`:

```js
function getUser() {
    return fetch("/api/user")
        .then(response => response.json())
        .then(user => {
            return user;
        })
        .catch(error => {
            console.error(error);
        });
}
```

With `async`/`await` (shown again for comparison):

```js
async function getUser() {
    try {
        const response = await fetch("/api/user");
        const user = await response.json();
        return user;
    } catch (error) {
        console.error(error);
    }
}
```

The second version is generally considered easier to read, since it avoids nested `.then()` chains and consolidates error handling in one `catch` block, similar to synchronous code.

### 15.34 Promise Chaining

Promises are especially powerful because `.then()` itself **returns another Promise**, allowing chains of dependent asynchronous steps:

```js
fetch("/api/user")
    .then(response => response.json())
    .then(user => {
        return fetch(`/api/movies/${user.id}`);
    })
    .then(response => response.json())
    .then(movies => {
        console.log(movies);
    })
    .catch(error => {
        console.error(error);
    });
```

```
fetch user
   ↓
response
   ↓
parse JSON
   ↓
user
   ↓
fetch movies
   ↓
response
   ↓
parse JSON
   ↓
movies
```

### 15.35 Why Does .then() Return a Promise?

```js
const p1 = Promise.resolve(10);

const p2 = p1.then(value => {
    return value * 2;
});
```

`p1` is fulfilled with `10`. `p2` is fulfilled with `20`. The **return value of the `.then()` callback becomes the fulfillment value of the new Promise** that `.then()` produces — this is exactly what makes chaining `.then()` calls meaningful.

### 15.36 What if .then() Returns Another Promise?

```js
const p1 = Promise.resolve(10);

const p2 = p1.then(value => {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(value * 2);
        }, 1000);
    });
});
```

In this case, `p2` doesn't just get "a Promise" as its value — it automatically **waits for** the inner Promise to settle, then adopts that result:

```
p1
 ↓
10
 ↓
then callback
 ↓
new Promise
 ↓
wait 1 second
 ↓
20
 ↓
p2 fulfilled with 20
```

This automatic "flattening" behavior is precisely what makes chained asynchronous steps (as in 15.34) work correctly, without ending up with a Promise nested inside a Promise.

### 15.37 Sequential vs Parallel Asynchronous Operations

```js
const user = await getUser();
const movies = await getMovies();
```

This is inherently **sequential**:

```
getUser()
   ↓
wait
   ↓
getMovies()
   ↓
wait
   ↓
continue
```

`getMovies()` doesn't even *start* until `getUser()` has fully finished.

### 15.38 But What If the Operations Are Independent?

If two operations don't actually depend on each other's results, awaiting them one after another wastes time unnecessarily:

```js
const genres = await getGenres();
const actors = await getActors();
```

A better approach is to **start both immediately**, then await them:

```js
const genresPromise = getGenres();
const actorsPromise = getActors();

const genres = await genresPromise;
const actors = await actorsPromise;
```

Because calling `getGenres()` and `getActors()` (without awaiting immediately) starts both operations right away — the `await` only pauses for the *result*, not for the operation to *begin*.

### 15.39 Promise.all()

A cleaner way to express "run these independent operations in parallel and wait for all of them":

```js
const [genres, actors] = await Promise.all([
    getGenres(),
    getActors()
]);
```

```
             ┌── getGenres()
             │
START ───────┤
             │
             └── getActors()
             
             ↓
          wait for
          both
             ↓
          continue
```

If `getGenres()` takes 2 seconds and `getActors()` takes 3 seconds, `Promise.all()` takes roughly **3 seconds total** (the duration of the slowest operation) — not 5 seconds, because the two operations genuinely run concurrently rather than one after another.

### 15.40 Why Promise.all() Is Faster — Visualized

**Sequential:**

```
0s ───── 2s ───────────── 5s
     genres      actors
```

**Parallel (via Promise.all()):**

```
0s ───────────── 3s
     genres
     actors
```

Both operations begin at roughly the same time, so the total wait is bounded by the *slower* of the two, not their *sum*.

### 15.41 Promise.all() Behavior and Rejection

```js
const result = await Promise.all([
    promise1,
    promise2,
    promise3
]);
```

If **all** succeed, `result` is an array containing each Promise's fulfilled value, **in the same order** the Promises were passed in (regardless of which one actually finished first):

```js
const result = await Promise.all([
    Promise.resolve("A"),
    Promise.resolve("B"),
    Promise.resolve("C")
]);
// ["A", "B", "C"]
```

If **any one** of the Promises rejects, `Promise.all()` itself immediately rejects with that error — even if the other Promises are still pending or would have eventually succeeded. This "fail-fast" behavior is an important consideration when deciding whether `Promise.all()` is the right tool versus alternatives that tolerate individual failures (such as `Promise.allSettled()`).

---

## Summary

This guide walked through the JavaScript language end-to-end: what the language is and how engines execute it; its dynamic type system and primitives; objects and arrays; variable declarations and scoping; functions and control flow; the modern array/object syntax that dominates contemporary code; the object model, prototypes, classes, and `this`; closures and the execution-context model that underlies them; the browser's DOM and event system; the fundamentals of asynchronous programming; the module system and Node/npm ecosystem; a rigorous treatment of hoisting and the Temporal Dead Zone; and finally a deep, traced-through treatment of the event loop, microtasks vs. tasks, Promises, and `async`/`await`. Together these form the conceptual foundation for virtually all practical JavaScript work, from vanilla DOM scripting to modern frameworks and Node.js backends.
