**Thread Of Execution and CallStack:**

![image.png](attachment:2e58b9c4-fc3c-4f76-8ee9-6569e8053672:image.png)

![image.png](attachment:b20902a1-ed26-4f87-b21c-e1466f151eef:image.png)

### **Scope changing and Lexical Scoping:**

Lexical scoping and scope chaining are **fundamental concepts in JavaScript that determine how variables are accessed within nested functions**. Lexical scoping (or static scoping) means that a variable's accessibility is determined by its position in the source code, specifically where it's defined, not where it's called. The scope chain is the mechanism JavaScript uses to find the value of a variable by searching through nested scopes, starting with the innermost scope and moving outwards to its parent scopes until the variable is found or the global scope is reached.

Lexical scope is an area where we can access our variables.
When a variable is accessed, JavaScript looks for it in the current function's scope. If not found, it moves up to the outer function's scope (the lexical environment it references), and so on, all the way up to the global scope. This chain of scopes is the scope chain. 

## **Code:**

```
function outerFunction() {
    let outerVar = "I'm from outerFunction";

    function innerFunction() {
        let innerVar = "I'm from innerFunction";
        console.log(outerVar); // Can access outerVar due to lexical scope
    }

    innerFunction();
    // console.log(innerVar); // ❌ Error: innerVar is not defined here
}

outerFunction();
```

Callback Hell, also known as the "Pyramid of Doom," is a situation in JavaScript where multiple asynchronous operations are chained together using deeply nested callback functions. This typically occurs when one asynchronous operation depends on the completion or result of a previous one, leading to a cascade of nested callback functions within callback functions.

# **Why callback Hell Accure:**

Callback hell occurs **due to the nested nature of callbacks used for handling asynchronous operations in programming**. When multiple asynchronous tasks need to be completed in sequence, and each task's completion triggers another, the code can become deeply nested, resembling a pyramid structure. This nesting leads to reduced readability, difficulty in maintaining the code, and error handling challenges. 

## **Code:**

****

```
getUser(function (user) {
    getPosts(user.id, function (posts) {
        getComments(posts[0].id, function (comments) {
            sendNotification(comments[0], function (response) {
                console.log("Notification sent!");
            });
        });
    });
});
```

## **SameSite or Http Only Cookies:**

HTTP-only and SameSite are **attributes used with cookies to enhance web security**. HTTP-only cookies, as the name suggests, are not accessible to client-side scripts like JavaScript. This prevents potential Cross-Site Scripting (XSS) attacks where malicious scripts could steal sensitive cookie information. SameSite, on the other hand, controls how cookies are sent in cross-site requests, mitigating Cross-Site Request Forgery (CSRF) attacks.

```

res.cookie('sessionId', 'abc123', {
  httpOnly: true,
  secure: true,
  sameSite: 'strict'
});

```

**How Closures Can Cause Memory Leaks and What You Can Do About It:**

In JavaScript, when you create a function **inside another function**, the inner function can **remember** the variables from the outer one — **even after** the outer function has finished running.

****

```
function outerFunction() {
    let name = "Ali";
    return function innerFunction() {
        console.log(name); // Still remembers 'Ali'
    };
}

let myFunc = outerFunction();
myFunc(); // Output: Ali

```

## **How Can a Closure Cause a Memory Leak?**

A **memory leak** happens when your app keeps **holding onto data** that it **doesn’t need anymore**, so the GC **can’t clean it up**.

If a **closure is remembering a large object**, like a big array or object, **even when it’s no longer needed**, it will **stay in memory** — this causes a memory leak.

```cpp
function createLeak() {
    const largeData = new Array(1000000).fill('data');

    return function leakyFunction() {
        console.log(largeData[0]); // Only using one value
    };
}

const leaky = createLeak();
```

**How to Fix It:**

```cpp
function createFixed() {
    const largeData = new Array(1000000).fill('data');
    const neededValue = largeData[0];

    return function fixedFunction() {
        console.log(neededValue); // Only keeping what we need
    };
}

const fixed = createFixed();

```

Now the closure **only keeps the small value** (`neededValue`) — not the whole array. So the rest of the array **can be cleaned up** by the Garbage Collector.

### **JavaScript is a Single Threaded Language:**

JavaScript is inherently a single-threaded language. This means that, at its core, JavaScript executes code in a single sequence, processing one task at a time. This single-threaded nature applies to how JavaScript handles its call stack and executes program instructions.

## **Call Stack**

### 🔹 What is the Call Stack?

The **call stack** is like a **to-do list** for JavaScript — it keeps track of **which function is currently running** and **what should run next**.

You can think of it like a **stack of plates**:

- You add (push) a new plate (function) on top.
- You remove (pop) the top plate once you're done.

```cpp
function greet() {
  console.log("Hello");
}

function start() {
  greet();
  console.log("Welcome");
}

start();
```

**Event Loop:**

![image.png](attachment:c2b0c458-6b15-493d-8aad-4a35af7cce85:image.png)

📦 1. **Call Stack** (Left side)

---

- This is **where your JavaScript code runs** line-by-line.
- Example: If you write `console.log("Hello")`, it goes on this stack.
- Code is executed **from top to bottom** in the stack.
- Once finished, the task is removed from the stack.

---

### 🌐 2. **Web APIs** (Right side)

- These are **functions provided by the browser** (like `setTimeout`, `fetch`, or interacting with the DOM).
- They handle **long tasks** (e.g., waiting 2 seconds, fetching data from a server).
- Once finished, their results are sent to the appropriate queue.

---

### 🟢 3. **Priority Queue** (Middle bottom)

- This queue stores **microtasks**, mostly from **Promises**.
- These tasks are considered **high priority**.
- After the call stack is empty, JavaScript will first check this queue and run its tasks **before anything else**.

---

### 🟩 4. **Callback Queue** (Middle top)

- This queue stores **other tasks** like `setTimeout`, `onclick` events.
- These are **lower priority** than Promises.
- The event loop checks this **after** the Priority Queue.

---

### 🔁 5. **Event Loop** (Circular arrows)

- The **event loop** is like a manager.
- It keeps checking:
    - "Is the Call Stack empty?"
    - If yes, it pushes tasks from the **Priority Queue first**, then from the **Callback Queue** into the stack.

---

### 🧠 How it works in action:

1. You run your code → it goes on the **Call Stack**.
2. If it uses `setTimeout`, `fetch`, or `Promise`, they are handled by **Web APIs**.
3. Once those are done, results go to **queues** (Promises to **Priority**, `setTimeout` to **Callback**).
4. When the Call Stack is empty, the **Event Loop** moves tasks from queues back to the **Call Stack** to run.

---

### 📌 Key Point:

- **Promises (Priority Queue)** are always handled **before** things like `setTimeout` or `onclick` (Callback Queue).
- That’s why `Promise.then()` runs before a `setTimeout`.

---

## **De-Bouncing:**

Debouncing in JavaScript is a technique used to control the rate at which a function is executed, particularly in response to frequent events like user input (e.g., typing in a search bar), window resizing, or scrolling. Its primary purpose is to prevent excessive function calls and improve application performance and responsiveness. 

"Wait until the user **stops typing for a moment**, then do something."

Code:

```cpp

// Debounce function: delays the execution of `func` until after `delay` ms of no input
function debounce(func, delay) {
    let timer;
    return function (...args) {
        clearTimeout(timer); // cancel previous timer
        timer = setTimeout(() => func.apply(this, args), delay); // set new timer
    };
}

// Get DOM elements
const searchInputMD = document.getElementById("searchInputMD");
const outputMD = document.getElementById("outputMD");

// Function to handle search logic
function handleSearchMD(event) {
    const value = event.target.value;
    console.log("Searching for:", value);
    outputMD.textContent = `Searching for: ${value}`;
}

// Attach debounced event listener to input field
searchInputMD.addEventListener("input", debounce(handleSearchMD, 500));

```

## **Null vs Undefined**

- **`null`** → You **manually** set a variable to "nothing."
- **`undefined`** → JS assigns it when you declare a variable without a value.

```jsx
let a=null;
let b;
console.log(a);
console.log(b);
```

## **NaN (Not a Number)**

`NaN` stands for "Not-a-Number". It is a special numeric value that represents the result of an operation that cannot produce a valid number.

```jsx
let x = "hello" * 2;
console.log(x); // NaN
console.log(typeof x); //Number
```

## **Variable Hoisting**

JS moves declarations to the top.

```jsx
console.log(a); // undefined (hoisted)
var a = 5;

console.log(b); // Error: Cannot access before initialization
let b = 10;

```

# **var vs let vs const**

```jsx
var x = 1; // function-scoped
let y = 2; // block-scoped
const z = 3; // block-scoped, cannot change

x = 10;
y = 20;
// z = 30; // ❌ Error

```

# **Data Types**

- **Permitive**: Number, String, Boolean, Undefined, Null, Symbol, BigInt
- **Non-Primitive**: Object, Array, Function

```jsx
let fruits = ["apple", "banana", "mango"];
fruits.forEach(fruit => console.log(fruit));

```

# **6.Type Conversion**

```jsx
 let str = "123";
let num = Number(str); // 123
console.log(typeof num); // "number"

let invalid = Number("abc");
console.log(invalid); // NaN
```

# **typeof Operator**

```jsx
console.log(typeof 5); // "number"
console.log(typeof "hi"); // "string"
console.log(typeof null); // "object" (JS quirk)

```

## **Array Methods (map, filter)**

```jsx

let nums = [1, 2, 3];

// map - double each number
let doubled = nums.map(n => n * 2);
console.log(doubled); // [2, 4, 6]

// filter - get even numbers
let evens = nums.filter(n => n % 2 === 0);
console.log(evens); // [2]

```

# **Event Bubbling vs Capturing**

- Bubbling: event goes from child to parent.
- Capturing: event goes from parent to child.

```jsx
<div id="parent">
  <button id="child">Click me</button>
</div>

<script>
  document.getElementById("parent").addEventListener("click", () => {
    console.log("Parent clicked");
  });

  document.getElementById("child").addEventListener("click", () => {
    console.log("Child clicked");
  });
</script>
```

# **Higher-Order Functions:**

- Takes or returns other functions.
- Example: `map()`, `filter()`, `reduce()`.

```jsx
function greet(name) {
  return function(message) {
    console.log(message + ", " + name);
  };
}

const greetJohn = greet("John");
greetJohn("Hello"); // "Hello, John
```

# **IIFE (Immediately Invoked Function Expression)**

- A function that runs right away.
- Wrapped in `()` and called immediately.

```jsx
(function() {
  console.log("Runs right away!");
})();

```

# **Closures**

- Function remembers outer variables.
- Useful for private data and counters.

Function "remembers" variables.

```jsx
javascript
CopyEdit
function makeCounter() {
  let count = 0;
  return function() {
    return count++;
  };
}

const counter = makeCounter();
console.log(counter()); // 0
console.log(counter());

```

# **setTimeout & setInterval**

### . **setTimeout & setInterval**

- `setTimeout` runs once after delay.
- `setInterval` runs repeatedly.

```jsx

setTimeout(() => {
  console.log("Runs once after 2 seconds");
}, 2000);

let count = 0;
let timer = setInterval(() => {
  console.log("Interval: " + count);
  if (++count === 3) clearInterval(timer);
}, 1000);

```

# **Promises**

- Handle async code (like fetch).
- States: pending, resolved, rejected.

```jsx
const newPromise=new Promise((reject,resolve)=>{
  let x = 0;
   if (x == 0) {
    myResolve("OK");
  } else {
    myReject("Error");
  }
})

fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));

```

# **call, apply, bind**

```jsx
Function prototype call apply and bind are all methods that are used to control this context 
when calling a function
```

- Change `this` value in a function.
- `call`/`apply` run now; `bind` returns new function.

```jsx
function greet() {
  console.log("Hello " + this.name);
}

const person = { name: "Alice" };

greet.call(person);  // Hello Alice
greet.apply(person); // Hello Alice
const boundGreet = greet.bind(person);
boundGreet();        // Hello Alice

```

# **Event Delegation**

- Use one listener for many child elements.
- It uses event bubbling.

```html
<ul id="list">
  <li>Item 1</li>
  <li>Item 2</li>
</ul>

<script>
  document.getElementById("list").addEventListener("click", (e) => {
    if (e.target.tagName === "LI") {
      alert(e.target.textContent);
    }
  });
</script>
```

# **async/await vs Promises**

- `async/await` makes async code cleaner.
- Built on top of Promises.

```jsx
// With async/await
async function getData() {
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts/1");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
getData();

```

# **Array.reduce()**

- Reduces array to one value.
- Good for sums, averages, etc.

```jsx
let sum = [1, 2, 3].reduce((total, num) => total + num, 0);
console.log(sum); // 6

```

# **Currying**

- Turns multi-arg function into nested single-arg ones.
- Example: `add(2)(3)` gives 5.

```jsx
function add(a) {
  return function(b) {
    return a + b;
  };
}

console.log(add(2)(3)); // 5

```

# Generator Functions

- Functions that pause and resume.
- Use `yield` to pause, `next()` to resume.

```jsx

function* counter() {
  let i = 0;
  while (true) {
    yield i++;
  }
}

const gen = counter();
console.log(gen.next().value); // 0
console.log(gen.next().value); // 1

```

# Shallow vs Deep Copy

- Shallow: copies only top level.
- Deep: full copy of all levels.

```jsx
let obj = { name: "John", address: { city: "NY" } };

// Shallow copy
let shallow = { ...obj };
shallow.address.city = "LA";
console.log(obj.address.city); // "LA"

// Deep copy
let deep = JSON.parse(JSON.stringify(obj));
deep.address.city = "SF";
console.log(obj.address.city); // "LA"

```

# Observer Pattern (Simple Example)

- One updates many subscribers when data changes.
- Used in events, UI updates, etc.

```jsx
class Observer {
  constructor() {
    this.subscribers = [];
  }

  subscribe(fn) {
    this.subscribers.push(fn);
  }

  notify(data) {
    this.subscribers.forEach(fn => fn(data));
  }
}

const obs = new Observer();

obs.subscribe((data) => console.log("Subscriber 1:", data));
obs.subscribe((data) => console.log("Subscriber 2:", data));

obs.notify("Hello Observers!");

```

## **Cookies Vs Session**

In web development, both sessions and cookies are used to manage user data, but they differ significantly in how they store and handle information. **Cookies are small text files stored on the user's browser, while sessions store data on the server.**

# **Explain why JavaScript treats functions as first-class citizens.**

In JavaScript, functions are considered "first-class citizens" because they are treated like any other value or data type. This means you can perform the same operations on functions as you can on numbers, strings, or objects.

```
const greet = function(name) {
  console.log(`Hello, ${name}!`);
};

greet("Alice"); // Output: Hello, Alice!

```

1. `x` is a **primitive number** (`10`), not an object.
2. When you try to assign `x.prop = 'test'`, JavaScript:
    - Temporarily **wraps** the primitive in an object (`new Number(10)`).
    - Adds the property `prop` to that temporary object.
    - Immediately **discards** that wrapper object.
3. Later, when you try to access `x.prop`, JavaScript:
    - Wraps `x` in a **new** temporary object.
    - Looks for `prop` — which doesn't exist.
    - Returns `undefined`.

```jsx
let x = 10;
x.prop = 'test';
console.log(x.prop); // ??

```

## **Dynamic scoping**

Dynamic scoping is a concept in JavaScript that **determines the value of a variable based on where it's used, rather than where it's defined**. This behavior is different from lexical scoping, which is used in most programming languages and determines the value of a variable based on where it's defined. outerFunction();

```
var x = 1;

function printX() {
  console.log(x);
}

function foo() {
  var x = 2;
  printX(); // would log 2 in dynamic scoping
}

foo();
```

# **Weak Set And Set**

| Feature | `Set` | `WeakSet` |
| --- | --- | --- |
| Stores | Any type (primitives or objects) | **Only objects** (no primitives) |
| Garbage Collection Friendly | ❌ No | ✅ Yes — objects can be GC’ed |
| Iterable (`for...of`, etc.) | ✅ Yes | ❌ No |
| Size property | ✅ `.size` available | ❌ No `.size` |
| Can be enumerated | ✅ Yes | ❌ No |
| Use cases | General purpose unique storage | Caching or tracking objects |

```jsx
//set and week sets

const set=new Set();
set.add(1);
set.add([1,2,3,4,5,6,7]);
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
console.log(set.has(1));
// console.log(set.values());
console.log(set.delete(7));
// console.log(set.values());
for (let i of set) {
    console.log(i);
}

const weakSet = new WeakSet();
console.log(weakSet); // WeakSet {}

let obj = {
  message: "Hi",
  sendMessage: true,
};
weakSet.add(obj);

console.log(weakSet);
```

## What Is an Enumerable Property?

An **enumerable property** is a property that can be **iterated over** using:

- `for...in` loop
- `Object.keys()`
- `JSON.stringify()` (if it's an own enumerable property)

```jsx
 const obj = { name: "Alice" };

console.log(Object.keys(obj)); // ["name"]
for (let key in obj) {
  console.log(key); // "name"
}

```

## What Is a Non-Enumerable Property?

A **non-enumerable property** will **not appear** during those kinds of enumeration.

You can define it manually using `Object.defineProperty()`

```jsx
const obj = {};

Object.defineProperty(obj, 'secret', {
  value: 'hidden value',
  enumerable: false
});

console.log(obj.secret);            // "hidden value"
console.log(Object.keys(obj));      // [] ← "secret" is hidden
for (let key in obj) console.log(key); // nothing printed

```

# **Explain the differences between Function.prototype.call, Function.prototype.apply, and Function.prototype.bind.**

! `Function.prototype.call`, `apply`, and `bind` are three closely related methods in JavaScript that allow you to **control the `this` context** of a function.

| Feature | `call()` | `apply()` | `bind()` |
| --- | --- | --- | --- |
| Calls the function now? | ✅ Yes | ✅ Yes | ❌ No (returns new function) |
| Argument format | Comma-separated | Array or array-like | Comma-separated (partial) |
| Returns | Function's return value | Function's return value | A new bound function |
| Use case | Direct call with custom `this` | Same as `call`, but for arrays | Save for later with bound context |