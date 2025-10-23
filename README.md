# 📘 Lesson 6: Errors and Asynchronous JavaScript

---

## 🎯 Learning Objectives

By the end of this lesson, you will be able to:

* Handle runtime errors using **try...catch** and **throw**.
* Create and manage **Promises** in JavaScript.
* Understand **asynchronous execution** with `setTimeout()`.
* Use **async/await** to simplify asynchronous code.
* Understand the difference between **microtasks** and **macrotasks**.

---

## 🧩 1️⃣ Exception Handling — try, catch, throw

Errors are a normal part of programming. In JavaScript, they stop code execution unless handled properly. The `try...catch` structure helps us catch and respond to errors gracefully.

### Basic Example

```js
try {
  console.log("Before the error");
  nonExistentFunction(); // ❌ This will throw an error
  console.log("After the error"); // skipped
} catch (error) {
  console.log("⚠️ Caught an error:", error.message);
} finally {
  console.log("✅ Program continues...");
}
```

✅ **Output:**

```
Before the error
⚠️ Caught an error: nonExistentFunction is not defined
✅ Program continues...
```

### Custom Errors

We can raise our own errors using the `throw` keyword.

```js
function registerUser(username) {
  try {
    if (!username || username.trim() === "") {
      throw new Error("Username cannot be empty!");
    }
    console.log(`✅ User '${username}' registered successfully!`);
  } catch (err) {
    console.log("⚠️ Error:", err.message);
  }
}

registerUser("Alex"); // Works
registerUser("");   // Triggers custom error
```

---

## ⏱️ 2️⃣ Asynchronous Execution and the Event Loop

JavaScript runs **synchronously** by default — line by line. But with asynchronous functions like `setTimeout()`, code can continue executing while waiting for other tasks.

### Basic Example

```js
console.log("Start");
setTimeout(() => console.log("Inside timeout (2s)"), 2000);
console.log("End");
```

✅ **Output:**

```
Start
End
Inside timeout (2s)
```

The code after `setTimeout` runs immediately — JavaScript doesn’t wait.

### Multiple Timeouts

```js
console.log("A");
setTimeout(() => console.log("B (after 2s)"), 2000);
setTimeout(() => console.log("C (after 1s)"), 1000);
console.log("D");
```

✅ **Output:**

```
A
D
C (after 1s)
B (after 2s)
```

### 0ms Delay — Still Asynchronous!

```js
console.log("Start");
setTimeout(() => console.log("Timer 1"), 0);
setTimeout(() => console.log("Timer 2"), 0);
console.log("End");
```

✅ **Output:**

```
Start
End
Timer 1
Timer 2
```

Even a `0ms` delay means the callback runs **after** synchronous code.

---

## ☕ 3️⃣ Simple Async Example — Coffee Brewing

```js
console.log("Preparing...");
setTimeout(() => {
  console.log("Your coffee is ready ☕");
}, 3000);
console.log("Please wait while we brew your coffee...");
```

✅ **Output:**

```
Preparing...
Please wait while we brew your coffee...
Your coffee is ready ☕
```

This shows how JavaScript schedules tasks without pausing the program.

---

## 🧠 4️⃣ Promises — Managing Asynchronous Code

A **Promise** represents something that will finish in the future — it may **resolve** (success) or **reject** (fail).

### Creating a Simple Promise

```js
const myPromise = new Promise((resolve, reject) => {
  resolve("✅ Task completed successfully!");
});
```

### Using the Promise

```js
myPromise.then((message) => console.log(message));
```

✅ Output:

```
✅ Task completed successfully!
```

### Adding Resolve and Reject

```js
const checkConnection = new Promise((resolve, reject) => {
  const online = true; // try false
  if (online) resolve("🌐 Internet connected!");
  else reject("🚫 No internet connection!");
});

checkConnection
  .then((msg) => console.log(msg))
  .catch((err) => console.log(err));
```

### Promises with a Delay

```js
const fetchWeather = new Promise((resolve, reject) => {
  console.log("Fetching weather data...");
  setTimeout(() => {
    const success = true; // change to false to simulate failure
    if (success) resolve("☀️ Weather: Sunny");
    else reject("🌧️ Failed to fetch weather!");
  }, 2000);
});

fetchWeather
  .then((data) => console.log(data))
  .catch((err) => console.log(err));
```

---

## ⚙️ 5️⃣ Async / Await — Cleaner Promise Syntax

`async` and `await` make Promise code look like normal synchronous code.

### Example

```js
const makeCoffee = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = true; // change to false to test reject
    if (success) resolve("☕ Coffee is ready!");
    else reject("❌ Machine error!");
  }, 2000);
});

async function serveCoffee() {
  try {
    const message = await makeCoffee; // Wait for promise to resolve
    console.log(message);
  } catch (error) {
    console.log(error);
  }
}

serveCoffee();
```

✅ **Output:**

```
☕ Coffee is ready!
```

❌ (if failure)

```
❌ Machine error!
```

### Difference Between `.then()` and `await`

| Feature        | `.then()`      | `await`                |
| -------------- | -------------- | ---------------------- |
| Syntax Style   | Uses callbacks | Looks like normal code |
| Error Handling | `.catch()`     | `try...catch`          |
| Where to use   | Anywhere       | Inside async functions |

---

## 🔄 6️⃣ Microtasks vs Macrotasks

When async code finishes, callbacks are added to different queues:

* **Microtasks**: Promises, `queueMicrotask()`
* **Macrotasks**: `setTimeout()`, `setInterval()`

### Example

```js
console.log("Start");

setTimeout(() => {
  console.log("⏱️ setTimeout callback (Macrotask)");
}, 0);

const makeCoffee = new Promise((resolve) => {
  console.log("Brewing coffee...");
  resolve("☕ Coffee ready!");
});

makeCoffee.then((msg) => console.log("🧩 Promise callback (Microtask):", msg));

console.log("End");
```

✅ **Output:**

```
Start
Brewing coffee...
End
🧩 Promise callback (Microtask): ☕ Coffee ready!
⏱️ setTimeout callback (Macrotask)
```

### Key Takeaway

| Task Type | Example    | Queue           | Priority  |
| --------- | ---------- | --------------- | --------- |
| Microtask | Promises   | Microtask Queue | 🔺 Higher |
| Macrotask | setTimeout | Callback Queue  | 🔻 Lower  |

**Microtasks always run before Macrotasks** once the call stack is clear.

---

## 🧩 7️⃣ Summary

| Concept             | Description                 | Example                        |
| ------------------- | --------------------------- | ------------------------------ |
| try/catch           | Handle runtime errors       | `try { risky() } catch(e) {}`  |
| throw               | Create custom errors        | `throw new Error('Bad input')` |
| setTimeout          | Run code asynchronously     | `setTimeout(fn, 1000)`         |
| Promise             | Represents a future result  | `new Promise(resolve, reject)` |
| .then()/.catch()    | Handle success/failure      | `promise.then(fn).catch(fn)`   |
| async/await         | Cleaner way to use Promises | `await promise`                |
| Microtask/Macrotask | Event loop scheduling       | Promises vs setTimeout         |

---

### 💬 Final Thought

> JavaScript may be single-threaded, but with **Promises, async/await, and the event loop**, it can handle multiple things at once — efficiently and elegantly!
