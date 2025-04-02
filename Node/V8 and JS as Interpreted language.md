How Node execution work:

- Node is a C++ code that takes input
- Input is a JS file.
- Node interpret the JS.
- JS code is executed serially until it is done.

But there are situations where programs does not really end( timers ) or code execution takes lots of time (I/O operation). Take these situations
- Timers
- File I/O
- Network I/O

We have to check and check and check for a response and it can come at any moment.

That's why we need **Event Loop**.

## That Loop

- A loop with phases.
- Single Tread.
- Asynchronous non-blocking I/O.
- Each phase has queue of callbacks.
- terminates when there is no call back left or nothing to do.

---
## Main Module

This is the initial phase

- Main module executes synchronously
- No callbacks get executed
- Main loop not yet initialized
- Initial phase
- Runs once

```js
int x = 0;

setTimeout(() => console.log("Should run after 1 ms"), 1);

for(let i = 0; i < 100000000; i++) {
	// Does something
}

console.log("Will run first as it's run in initial phase");
```

So, suppose the loop takes 3 secs to run, then `setTimeout` should run first right?

Well, that's not the case as timers are not executed in the initial phase.

### Modules load before main module

- Modules are resolved first
- Before running the main modules
- Code of these modules run first before main module.
- `import` statement are asynchronous in nature. So, that's a different case.
- Modules can load other modules

```js
// <---------------Main module------------------>
import "a.mjs"
console.log("Main module about to start");

// does something

console.log("Main module end")


// <-------------------a.mjs---------------------->

console.log("In a.mjs, about to shart running");

// code

console.log("In a.mjs, at the end");

// <--------------------------------------------->
// <-----------------Output---------------------->
// <--------------------------------------------->

/**
*   In a.mjs, about to shart running
*   In a.mjs, at the end
*   Main module about to start
*   Main module end
*/
```


----
## Timer phase

**First phase of Event Loop**

So how can do timers? A code that runs after a certain amount of time is passed.

1. Run a loop that runs continuously to check if that threshold amount of time has passed(Really bad approach).
2. Use OS sleep call to sleep the process, accurate for 90% of time.(Node uses this approach).
3. Use hardware timer. Highest accuracy among.

So now lets talk about timers
- Runs after initial phase
- Done by libuv
- Timer callbacks are **scheduled** and **sorted** by duration
- **Not always accurate:** Can be slowed down by other phases.

```js
const timerCallback = (a) => {
	console.log(`The timer was supposed run down ${a} ms later, but it as run on ${Date.now() - startTime} ms later, which means it was delayed by ${Date.now() - startTime - a} ms`)
}
const startTime = Date.now();

// Third argument is passed to timerCallback function
setTimeout(timerCallback, 0, 0)
setTimeout(timerCallback, 1, 0, )
setTimeout(timerCallback, 100, 100)
setTimeout(timerCallback, 300, 300)

for(let i = 0; i < 100_000_000; i++) {}
```

---
## Pending callbacks

**Second phase of Event loop**

- For delayed callbacks(TCP, UDP errors)
- This phase is for callbacks with less priority
- Examples are TCP errors, network delays
- Deferring it so try later may fix it, avoid unnecessary retries( Not always though)

```js
//example where pending callbacks are scheduled in a different phase. 
const net = require('net');
const fs = require(`fs`);
 
console.log(`START`);
// Define the IP and port
//change the host to a none existant port host, 
//the ECONNREFUSED will be queued in the pending callbacks phase output will be 
/*START
END
time out
Connection error: connect ECONNREFUSED 127.0.0.1:80
Connection closed
readFileCallback hello world
*/
//but if you change it to a valid ip/port say google's 
//the output will be 
/*
START
END
time out
readFileCallback hello world
Connected to server at 142.250.188.23:80
*/



// Create a client
const clientFail = new net.Socket();
const clientSuccess = new net.Socket();

// Connect to the server (fails)
clientFail.connect(9999,'192.168.4.21', () => {
    console.log(`Connected to server at ${'192.168.4.21'}:${9999}`);
    //👆🏻👆🏻👆🏻 Will be placed in pending callback phase as it fails
});
// Connect to the server that exists (this is example.com)
clientSuccess.connect(80, '93.184.215.14', () => {
    console.log(`Connected to server at ${'93.184.215.14'}:${80}`);
    //👆🏻👆🏻👆🏻 Will be placed in poll phase as it succedes
});

// Handle errors
clientFail.on('error', (err) => {
    console.error(`Connection error: ${err.message}`);
});
// Handle errors
clientSuccess.on('error', (err) => {
    console.error(`Connection error: ${err.message}`);
});


//slow down the initial phase
for (let i = 1; i <= 500000000; i++);
//after the loop ends and initial phase is done, we enter the poll phase and actually read the file (no callbacks ready)
setTimeout(()=>console.log("timer!"), 0)
console.log(`END`);

```

There is some exceptions:
- If address is look back address ( `127.0.0.1` ), it takes priority ( poll phase ).

## Idle, Prepare phase

**The invisible phase**

- Two steps in one phase, used by lib_uv
- Node maps this phase to lib_uv
- Not exposed to modules
- Can be over written with C++ Node extension

#### Idle

- Idle executes every iteration
- It still runs every iterations
- Internal tasks
- Doesn't mean that it only when node is idle

#### Prepare


---
## Poll phase

**Most important phase in NodeJS**

Two things happen in this phase

##### First: I/O being pulled
- Listen on sockets
- Read/Write connections
- Accept connections
- Read/Write files

##### Second: Callbacks are executed
- onRead -> If file finished reading
- onConnect -> If a connections is established do this
- onListen -> If a socket is listening, do this

**Dynamic Import:** These are considered IO. So, they are also executed on poll phase. They are delayed until they are needed and read from a file. So they are considered IO.


### Blocking

Node does blocking in Poll phase
For example, a file is being read from the storage, so blocking happen.

But incase of network IO, we don't know when response will be read.
`epoll_wat` system call manages blocking and has a timeout builtin.

#### How to does IO Work on Node?
- IO is completely asynchronous in Node
- Enabled by lib_uv library
- Network IO is implemented different from file IO
- Which depends on Kernel

### Summary

- Poll phase executes IO events and callbacks
- IO callbacks can be executed by any phase.
- IO can be blocking and non-blocking.
- Node tries to not to block if there are other work.

----
## Check phase

**Phase to execute things while waiting**

- Code purely exist for programer to get consistent output or program executions.
- Execute code right after poll phase or when poll phase is busy waiting.
- Always after poll phase and before timer.
- `setImmediate` is used.

```js
console.log("start");
const fs = require("node:fs");

const readCallback = (err, data) => {
	 console.log(`readCallback: ${data}`);
}

const f = "file.txt";

fs.readFile(f, readCallback);

setImmediate(() => {
	console.log("setImmediate");
})

console.log("end");
```


#### Case 1: File exists

Suppose `file.txt` contains `Hello World!`.

**Output:**

```
start
end
setImmediate
readCallback: Hello World!
```

In this case, the node execution process is:

1. **Main module execution:** 
    - First main module runs.
    - `fs.readFile` opens the file but does not read it.
    - `fs.readFile` delegates the file reading to the **I/O thread pool (libuv)**( poll phase ) and moves on.
    - `setImmediate` is scheduled( check phase ).
    - Event loop begins after this.
2. **Timer phase(1st):** 
    - No timer are scheduled, moves to next phase.
3. **Pending callback phase(1st):** 
    - This phase handles callbacks from I/O operations that were deferred in the previous event loop iteration, but nothing exists yet, so it moves on.
4. **Idle, prepare phase(1st):**
    - Node internal process happens.
5. **Poll phase(1st):** 
    - Node checks if `readFile` operation is complete or not(which is most likely not).
6. **Checking phase(1st):** 
    - `setImmediate` is runs and logs "setImmediate".
7. **Close callbacks phase(1st)**: 
	- No close event callbacks exist, so nothing happens.
8. **Timer phase(2nd):** 
    - Still nothing
9. **Pending callbacks phase(2nd):**
	- Still nothing
10. **Idle, prepare phase(2nd):**
    - Internal process (If any).
11. **Poll phase(2nd):** 
    - - If the file reading is complete, the `readCallback` is added to the **I/O callback queue** to be executed in the **pending callbacks phase** of the next iteration.
    - If the file read is still not done, the event loop stays in the poll phase until it is. Unlike the last time, Node doesn't has anything to do in other phase, so it stays.

12. **All other phases nothing and Poll phase(3rd):**
    - Node sees callback in event queue and executes which prints "readCallback".


**Extra knowledge:** The poll phase **does not signal the start of reading** because reading has **already started** in a separate thread. Instead, the poll phase **checks** if the file read has completed. If the file read is **still in progress**, Node moves to the next phase (Check phase). If the file read **has finished**, the callback is placed in the event queue **for the next poll phase iteration**.

*Execution process:* [ChatGPT questions](https://chatgpt.com/share/67d58517-fb5c-8004-8ec6-6f15da4d8203)
*Callback placement:* [DeepSeek](https://chat.deepseek.com/a/chat/s/19ba42a6-9d89-433c-bd2b-4f44ec7230eb)

## Case 2: File doesn't exist

**Output:**

```
starte
end
readCallback: undefined
setImmediate
```

1. **Main Module Execution**:
    - `fs.readFile` is called, but since the file does not exist, an **error is detected synchronously** (before the operation is delegated to the libuv thread pool).
    - Node.js **immediately schedules** the `readCallback` with the error (`err` object) to be executed in the **pending callbacks phase** of the **current iteration** of the event loop.
2. **Event Loop (1st Iteration)**:
    - **Pending Callbacks Phase**: The `readCallback` is executed with the error → logs `"readCallback: <error>"`.
    - **Check Phase**: The `setImmediate` callback is executed → logs `"setImmediate"`.

---

## Close callback phase

**Where close callbacks are scheduled**

- Clean up phase where close events happen
- Connection termination is a IO operation so this happens in poll phase.
- Then close callback is scheduled in this phase.

---
## process.nextTick()

- Node is a C++ program, when jumping from C++ to JS program is called a 'tick'.
- `process.nextTick()` runs after each tick.
- Roughly after each phase
- Can be used to run most priority code.
- Promises are scheduled here too!
- Run after as soon as possible after current phase is finished.
