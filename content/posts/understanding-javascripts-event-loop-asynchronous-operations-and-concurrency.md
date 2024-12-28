---
title: "Understanding JavaScript's Event Loop, Asynchronous Operations and Concurrency"
draft: false
date: 2024-12-28T10:36:53.429Z
description: "This post delves into the core concepts of JavaScript's event loop, asynchronous operations, and concurrency, explaining how JavaScript handles non-blocking operations and executes multiple tasks efficiently. Learn the intricacies of JavaScript's single-threaded nature and how it manages complex operations with real-world examples."
categories:
  - Tech
  - JavaScript
tags:
  - JavaScript
  - Event Loop
  - Asynchronous Operations
  - Concurrency
  - Web Development
---

JavaScript is single-threaded in the sense that its execution environment (the JavaScript runtime) operates in a single-threaded model. This means JavaScript code runs in a single call stack, and only one task is executed at a time.

## How Asynchronous Operations Work

The asynchronous nature of JavaScript is achieved through an event-driven architecture that leverages:

1. **Event Loop**
2. **Callback Queue**
3. **Web APIs** or **Worker Threads**

### **JavaScript Runtime Architecture**

- **Call Stack:**
    
    Executes the code line-by-line in a LIFO (Last-In-First-Out) order. This is where the synchronous code runs.
    
- **Web APIs (or Node.js APIs):**
    
    These are the browser-provided or Node.js-provided APIs that handle asynchronous operations. Examples include:
    
    - `setTimeout` for delays
    - `fetch` for HTTP requests
    - `DOM events` like `click` or `keyup`
- **Callback Queue (or Task Queue):**
    
    Once an asynchronous task completes (e.g., an HTTP response is received), the callback function is placed in the Callback Queue.
    
- **Event Loop:**
    
    Continuously monitors the Call Stack and Callback Queue. If the Call Stack is empty, it dequeues a callback from the Callback Queue and pushes it to the Call Stack for execution.
    

### **Concurrency vs. Parallelism**

- **Concurrency:** JavaScript can handle multiple tasks concurrently via asynchronous operations but runs them one at a time.
- **Parallelism:** Achieved through Worker Threads, where actual separate threads perform work in parallel. Web Workers or Node.js Worker Threads enable this.

## Differences Between C and High-Level Languages

In high-level languages like JavaScript or Python:

- Callback functions are more abstract and do not require the programmer to deal directly with pointers.
- Features like closures and first-class functions make passing and using callbacks easier.

In C:

- You must manage memory and deal with raw function pointers explicitly.
- There are no closures or automatic context preservation.

Despite these challenges, callbacks in C are powerful and widely used, especially in systems programming and libraries.

## **What is Event Loop?**

An **event loop** is a programming construct that waits for and dispatches events or messages in a program. It is commonly used in asynchronous programming environments, such as JavaScript, Node.js, or GUI frameworks (e.g., GTK, Qt), to handle non-blocking tasks.

The event loop allows a program to perform tasks efficiently without waiting for blocking operations (e.g., I/O, network requests) to complete, by leveraging an architecture that processes events one at a time in a continuous loop.

### **How the Event Loop Works**

The event loop runs in a loop, constantly checking for and processing tasks (or "events") from various sources, such as timers, network requests, or user inputs.

### **Steps of an Event Loop**

1. **Execute Synchronous Code**:
    - The event loop starts by executing all synchronous code in the program. This is the main thread of execution.
2. **Check Queues (Tasks/Callbacks):**
    - After the synchronous code finishes, the event loop checks for pending tasks in the **Callback Queue** or **Microtask Queue** (like resolved Promises in JavaScript).
3. **Process Events:**
    - The event loop picks the next event (e.g., a timer callback, I/O completion callback) and executes its associated callback.
4. **Repeat:**
    - The loop continues, alternating between executing tasks and checking for new events.

### **Key Components in the Event Loop**

1. **Call Stack**:
    - Holds function calls to be executed in LIFO (Last-In-First-Out) order.
    - Only one task is executed at a time on the Call Stack.
2. **Event Queue (or Task Queue)**:
    - Holds events (e.g., I/O operations, timers) waiting to be processed.
    - When the Call Stack is empty, the event loop dequeues a task and pushes it onto the Call Stack.
3. **Web APIs (or System APIs)**:
    - Asynchronous operations (e.g., `setTimeout`, `fetch`, file I/O) are delegated to system APIs or threads outside the main thread.
4. **Microtask Queue**:
    - Holds tasks like resolved Promises. Microtasks have a higher priority than the Event Queue tasks.
5. **Event Loop**:
    - Monitors the Call Stack and Queues, pushing tasks into the Call Stack when it becomes empty.

### **How Concurrency Works in the Event Loop**

1. **Long-Running Tasks in Asynchronous Operations**:
    - If a long-running task is **asynchronous** (e.g., file I/O, HTTP requests, database queries), it is typically offloaded to a separate system thread or API (like Web APIs in the browser or libuv in Node.js).
    - The event loop continues processing other tasks while the long-running operation executes in the background.
2. **Blocking Tasks in Synchronous Code**:
    - If a task is synchronous (e.g., a `while` loop or CPU-intensive calculation), the event loop is **blocked** and cannot process other tasks until the Call Stack is cleared.

### Example

```jsx
fetch('https://api.example.com/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

is processed in the JavaScript runtime, involving the **Event Loop**, **Call Stack**, **Callback Queue**, and **Microtask Queue**:

### **Step-by-Step Execution Flow**

1. **Start Execution**:
    - The `fetch` function is called.
    - The browser or Node.js delegates the HTTP request to the underlying **Web API** or **libuv** in Node.js (not part of the JavaScript engine).
    - The `fetch` call itself is asynchronous, so it immediately returns a `Promise` object and moves to the next line.
2. **Call Stack**:
    - The `fetch` call is removed from the **Call Stack** once it hands off the request to the Web API.
    - At this point, the JavaScript engine continues executing other synchronous code.
3. **Web API Handles the Request**:
    - The HTTP request is processed outside the main thread (e.g., by the browser’s networking layer).
    - When the response is received, the Web API signals the completion and resolves the `Promise` with the response object.
4. **Promise Resolution and Microtask Queue**:
    - The `.then` callback for the `Promise` is placed in the **Microtask Queue** (since Promises are handled as microtasks).
5. **Event Loop Processes the Microtask**:
    - After the current task finishes and the **Call Stack** is empty, the Event Loop picks tasks from the **Microtask Queue** first.
    - The `.then(response => response.json())` callback is pushed onto the Call Stack and executed.
6. **Parsing the Response**:
    - The `response.json()` method is called to parse the response body. This is typically an asynchronous operation that also returns a `Promise`.
    - Once this Promise is resolved, its `.then` callback (`data => console.log(data)`) is added to the **Microtask Queue**.
7. **Logging the Data**:
    - The next `.then` callback is dequeued from the Microtask Queue and pushed onto the Call Stack.
    - The data is logged using `console.log(data)`.
8. **Error Handling** (if needed):
    - If any error occurs (e.g., network failure or invalid JSON), the `.catch` callback is placed in the Microtask Queue and executed after all preceding `.then` callbacks.

### **Key Points**

1. **Promise Callbacks Are Microtasks**:
    - The `.then` and `.catch` callbacks are placed in the **Microtask Queue**, not the Callback Queue.
2. **Microtasks Have Higher Priority**:
    - The Event Loop always clears the Microtask Queue before processing tasks in the Callback Queue (e.g., `setTimeout` callbacks).
3. **Concurrency Without Blocking**:
    - The `fetch` operation is offloaded to the Web API, so the main thread remains free to execute other tasks.

## **Achieving Parallelism**

To handle CPU-bound tasks or achieve true parallelism, you need to use **Worker Threads** or **Web Workers**, which run tasks in separate threads.

## What is a Promise?

A **Promise** in JavaScript is an object that represents the eventual completion (or failure) of an asynchronous operation and its resulting value. It is a mechanism for managing asynchronous code, allowing developers to chain operations and handle results or errors more effectively than with traditional callback functions.

### **Promise Chaining**

Promises allow you to chain multiple `.then` calls, each processing the result of the previous operation.

### **Promise and Microtask Queue**

1. **What a Promise Represents**:
    - A Promise represents the result of an asynchronous operation:
        - **Fulfilled** (with a value).
        - **Rejected** (with an error).
        - **Pending** (neither fulfilled nor rejected yet).
2. **Connection to the Microtask Queue**:
    - When a Promise resolves (fulfilled or rejected), its associated callbacks (`.then`, `.catch`, `.finally`) are scheduled to run on the **Microtask Queue**.
    - Microtasks are a high-priority queue processed before tasks in the **Callback Queue** (used for `setTimeout`, `setInterval`, etc.).

### **Event Loop, Promises, and Microtask Queue**

1. **Event Loop Overview**:
    - The Event Loop processes tasks from the **Call Stack**.
    - If the Call Stack is empty, it first processes the **Microtask Queue**, then moves to the **Callback Queue**.
2. **Promises and Microtasks**:
    - A resolved/rejected Promise schedules its `.then`/`.catch` callback as a **microtask**.
    - Microtasks are executed immediately after the current task completes, but before moving to the Callback Queue.

## **How `async`/`await` Works with the Event Loop?**

1. When an `async` function encounters `await`, it pauses the execution of that function at the `await` point.
2. The Promise being `await`ed is sent to the **Microtask Queue**.
3. The JavaScript engine continues executing other synchronous and queued tasks.
4. Once the Promise is resolved or rejected, the function resumes execution from where it paused.

### **What Happens During `await`?**

1. When an `async` function encounters an `await` expression, it:
    - Pauses execution of the `async` function at that point.
    - Sends the Promise being `await`ed to the **Microtask Queue**.
    - Frees the **Call Stack** to allow other tasks (e.g., synchronous code or tasks in the Callback Queue) to execute.
2. Once the Promise is resolved (or rejected):
    - The function resumes execution from the point where it was paused.
    - If the Promise resolves, the value becomes the result of the `await` expression.
    - If the Promise rejects, an error is thrown, and it can be caught with a `try-catch` block or handled with `.catch`.

### **Behaviour in the Event Loop**

Here’s how the code fits into the **Event Loop**:

1. **Call Stack**:
    - Executes synchronous code: `console.log("Before calling asyncFunction")`, and the first part of `asyncFunction`.
2. **Microtask Queue**:
    - The Promise created by `await` is added to the **Microtask Queue**.
3. **Async Function Resumes**:
    - Once the Promise resolves, the `asyncFunction` resumes execution by processing the result of the `await`.

### **Does `await` Block the Main Thread?**

No, `await` **does not block the main thread**. While the `async` function is paused, the JavaScript runtime continues processing other tasks (e.g., user interactions, rendering, or other synchronous code).

### **How `async` functions work:**

When you use an **`async` function**, it returns a **Promise**, and it allows you to pause the execution of the function at the `await` keyword while waiting for the Promise to resolve.

**Key points**:

- The **`async` function** itself does **not** go directly into the **Microtask Queue**.
- When an `async` function encounters `await`, it **pauses** the function at that point and **schedules the continuation of the function** (the code after `await`) to the **Microtask Queue**.

So, when an **`async` function`** is used:

- The function **starts executing synchronously** until it hits an `await` statement.
- At the **`await`** point, the function **pauses** and the remaining code after `await` is added to the **Microtask Queue**.
- The event loop processes the **Microtask Queue** once the current synchronous code finishes.

## Event loop and Concurrency

The **event loop** in JavaScript processes **one task at a time**, but it ensures that the program remains **non-blocking** by efficiently managing the execution of tasks through different queues (such as the **Call Stack**, **Microtask Queue**, and **Callback Queue**).

Let me break it down more clearly:

### **How the Event Loop Works:**

1. **Call Stack**:
    - The **Call Stack** is where **synchronous code** gets executed.
    - When a function is called, it's pushed onto the stack and executed. When it finishes, it's popped off the stack.
    - If synchronous code runs, it **blocks** the event loop until the current function finishes, and no other tasks can be processed during that time.
2. **Microtask Queue**:
    - The **Microtask Queue** holds tasks that are queued after asynchronous operations (like **Promises** or **`async/await`**).
    - Microtasks are executed **after the current task in the Call Stack** finishes but **before any tasks in the Callback Queue**.
    - The event loop always processes the **Microtask Queue** first, before moving on to the **Callback Queue**.
3. **Callback Queue** (or **Task Queue**):
    - The **Callback Queue** holds tasks like `setTimeout()`, `setInterval()`, and events like user input or I/O operations.
    - These tasks are processed after the **Microtask Queue** has been emptied.

### **Event Loop Execution Flow:**

The event loop follows this sequence:

1. **Execute Synchronous Code**: The event loop starts by executing synchronous tasks from the **Call Stack**.
2. **Process Microtasks**: After a synchronous task finishes, it checks the **Microtask Queue** and executes any tasks (like `.then()` callbacks from Promises or `async/await` continuation).
3. **Process Callback Queue**: After the **Microtask Queue** is empty, the event loop moves to the **Callback Queue**, executing any pending tasks like `setTimeout()` or user event handlers.

### **Does the Event Loop Do One Task at a Time?**

Yes, in a sense, the event loop processes **one task at a time**, but it is important to understand that the event loop doesn’t **block** on asynchronous tasks. It just processes them in a non-blocking, event-driven manner by using the Call Stack, Microtask Queue, and Callback Queue.

Here’s the breakdown:

- **Synchronous code** (in the **Call Stack**) is executed first, blocking the thread until it's done.
- **Microtasks** (like Promise `.then()` callbacks) are processed after synchronous code, but before any other queued tasks.
- **Asynchronous callbacks** (like those from `setTimeout()`, events, etc.) are processed last.

### Key Concepts in Concurrency in JavaScript:

1. **Single-Threaded Execution**:
    - JavaScript runs on a **single thread** (one execution context at a time). The **Call Stack** processes tasks one by one.
    - This means that only one thing can be **actively executed** at any moment.
2. **Concurrency** in JavaScript:
    - Even though JavaScript is single-threaded, it achieves **concurrency** by handling tasks asynchronously using things like **Promises**, **`async/await`**, and **event handlers**.
    - **Concurrency** here means that multiple tasks are **managed** in a way that allows them to appear as if they are running simultaneously, but they are actually being executed one by one in a non-blocking manner.
3. **Event Loop**:
    - The **event loop** is key to this **non-blocking concurrency**. It allows JavaScript to continue processing other tasks (like UI updates, I/O events, and timeouts) while waiting for asynchronous tasks (like network requests or timers) to complete.
    - When a task is asynchronous (e.g., a network request or `setTimeout()`), JavaScript doesn't block the thread waiting for the result. Instead, it schedules the continuation of the task in the **Microtask Queue** or **Callback Queue**, allowing other tasks to execute in the meantime.
4. **Asynchronous Execution (Non-blocking)**:
    - JavaScript uses things like **Promises** and **`async/await`** to perform **asynchronous operations**, which allow the code to **pause** and continue later when the asynchronous task is done, without blocking the main thread.
    - This means that while JavaScript **waits** for one task (e.g., a network response), it can **process other tasks** in the meantime.

### Conclusion

Yes, JavaScript does enable **concurrency** (in the sense of **handling multiple tasks seemingly at the same time**), but it's done in a **single-threaded** manner, using techniques like the **event loop** and **asynchronous programming**. To clarify, **JavaScript itself** is **single-threaded**, but the **event loop** and its interaction with the **Call Stack**, **Microtask Queue**, and **Callback Queue** allow it to handle concurrency efficiently.

## **UI Changes**

UI updates—such as changing styles, adding/removing elements, or updating text—are handled in the **main thread** and are part of the **browser's render cycle**. Here’s how the interaction between JavaScript and UI updates works:

1. **JavaScript Manipulates the DOM**:
    - When you modify the text of an element or update the layout (like changing CSS properties), JavaScript directly interacts with the **DOM** (Document Object Model).
2. **Repaint & Reflow**:
    - **Reflow** occurs when changes to the DOM or CSS affect the layout (for example, when changing the size, position, or visibility of an element).
    - **Repaint** happens when visual changes don't affect the layout but require the browser to repaint the updated parts of the UI (e.g., changing the color or text).
    - The browser does these operations efficiently, and **they don’t block the event loop**. However, multiple DOM updates can trigger these processes multiple times, which could be a performance concern if done excessively (like in a tight loop).

## **Event Loop and Typing (Key Events)**

When you type in an input field (or trigger any other user interaction), the event loop is still in charge of handling everything, but JavaScript responds to the input in an efficient, non-blocking way.

1. **Input Events (Typing)**:
    - When you type, a **keyboard event** (like `keydown`, `keypress`, or `input`) is generated. These events are asynchronous because they don't block the rest of the code from executing.
    - The event handler (the function you attach to the event) is executed **asynchronously** and is placed in the **Event Queue** (not the **Microtask Queue**, because it's a UI event).
2. **Event Loop Handling**:
    - **The event loop is still running**, but it will prioritize processing events from the **Event Queue**, such as the `input` event generated by typing.
    - After JavaScript executes the current synchronous code (like your ongoing timer or other tasks), it will check the **Event Queue** and pick up the typing-related event to handle it.
    - **Even during long-running tasks or timers**, the event loop ensures **UI events** (like typing) are processed immediately by pulling them from the queue and executing the callback, ensuring that your app remains responsive.

### **Why Typing Doesn't Interrupt the Event Loop**:

- **Non-blocking Events**: JavaScript is designed to handle **user interactions** like typing asynchronously. Even if the event loop is processing other tasks, the event loop will make sure **input events** are handled **immediately** once the current stack is clear, allowing you to type without any interruption.
- **High Responsiveness**: The event loop ensures that tasks like user input are processed in **real-time**. Since events are added to the **Event Queue** and are handled right after the current synchronous code (and any microtasks), **typing can occur continuously without waiting for the current code to finish**, making it appear **instantaneous**.
- **UI Thread Priority**: Input events (like typing) are typically processed very quickly by the **UI thread**. This ensures that even though the event loop is processing other asynchronous tasks (like timers or network requests), the user’s typing experience remains responsive.

## How `Promise.all` Works

### What does "concurrent" mean in the context of `Promise.all()`?

- **Concurrency** generally refers to tasks that are **executed independently**, but not necessarily at the same time. In a **single-threaded environment**, concurrent tasks are managed by scheduling and switching between them. In contrast, **parallelism** refers to tasks being executed **at the same time**, potentially across multiple CPU cores or threads.

### How `Promise.all()` works:

1. **Parallel Execution**:
    - `Promise.all()` **does not block** the execution of any of the promises it takes in. It initiates all the promises concurrently (i.e., all promises are started at the same time), but the actual behavior of the promises depends on how they're set up.
    - For example, if you are fetching data from multiple URLs using `fetch()`, all these fetch operations are sent out **in parallel**. This is because `fetch()` is non-blocking and returns a promise that is resolved asynchronously.
2. **Non-blocking Asynchronous Operations**:
    - Inside the event loop, the JavaScript engine doesn't "pause" any ongoing tasks or promises. Instead, when you call `Promise.all()`, all the promises are initiated, and their callbacks (or `.then()`) are handled once the promises resolve (or reject).
    - Even though the promises are initiated simultaneously, the resolution of each promise happens asynchronously. That means they may finish at different times, but none of them block the others from running.

### **Is this Concurrent?**

- **Yes**, in a sense, `Promise.all()` handles **concurrent asynchronous operations** because it starts multiple promises and doesn’t wait for one to finish before starting the next one.
- But these tasks are **not truly parallel** (as they are still running on a single thread). They are concurrently executed using JavaScript's **non-blocking asynchronous mechanism**.

### The Key Difference: Sequential vs. Concurrent

- **Sequential (with `await` one by one)**:
    - Each promise waits for the previous one to finish before starting the next one.
    - It’s simpler and works well when the operations depend on each other (e.g., fetching data from one API and using that data to fetch from another API).
- **Concurrent (with `Promise.all()` and `await`)**:
    - All promises are started at once, and the program waits for **all of them** to finish before continuing.
    - This is much faster when the tasks are independent, as they run at the same time (concurrently).
