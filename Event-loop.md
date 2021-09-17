### What is the Event Loop?
The event loop is what allows Node.js to perform non-blocking I/O operations — despite the fact that JavaScript is single-threaded — by offloading operations to the system kernel whenever possible.

### Is Node.js completely single-threaded?
Node runs on a single thread, but some functions included in the Node.js standard library do not (the fs module functions, for example );

### libuv thread pool
This thread pool is composed of four threads used to delegate operations that are too heavy for the event loop. The above-mentioned long-running tasks in the event loop logic represent those operations described here as too expensive for the event loop.

![alt text](./EventLoop/EventLoop.png)

`Each phase has a FIFO queue of callbacks to execute. While each phase is special in its own way, generally, when the event loop enters a given phase, it will perform any operations specific to that phase, then execute callbacks in that phase's queue until the queue has been exhausted or the maximum number of callbacks has executed. When the queue has been exhausted or the callback limit is reached, the event loop will move to the next phase, and so on.

### Phases Overview
-   **timers**: this phase executes callbacks scheduled by  `setTimeout()`  and  `setInterval()`.
-   **pending callbacks**: executes I/O callbacks deferred to the next loop iteration.
-   **idle, prepare**: only used internally.
-   **poll**: retrieve new I/O events; execute I/O related callbacks (almost all with the exception of close callbacks, the ones scheduled by timers, and  `setImmediate()`); node will block here when appropriate.
-   **check**:  `setImmediate()`  callbacks are invoked here.
-   **close callbacks**: some close callbacks, e.g.  `socket.on('close', ...)`.`

#### Technically, the poll phase controls when timers are executed.

### pending callbacks
This phase executes callbacks for some system operations such as types of TCP errors.


### poll
The  **poll**  phase has two main functions:

1. Calculating how long it should block and poll for I/O, then
2. Processing events in the  **poll**  queue.
3. setImmediate()

### check

This phase allows a person to execute callbacks immediately after the  **poll**  phase has completed. If the  **poll**  phase becomes idle and scripts have been queued with  `setImmediate()`, the event loop may continue to the  **check**  phase rather than waiting.



## `setImmediate()`  vs  `setTimeout()`

`setImmediate()`  and  `setTimeout()`  are similar, but behave in different ways depending on when they are called.

-   `setImmediate()`  is designed to execute a script once the current  **poll**  phase completes.
-   `setTimeout()`  schedules a script to be run after a minimum threshold in ms has elapsed.


The order in which the timers are executed will vary depending on the context in which they are called.
if you move the two calls within an I/O cycle, the immediate callback is always executed first

The main advantage to using setImmediate() over setTimeout() is setImmediate() will always be executed before any timers if scheduled within an I/O cycle, independently of how many timers are present.

## `process.nextTick()`
`process.nextTick()` is not technically part of the event loop. Instead, the `nextTickQueue` will be processed after the current operation is completed, regardless of the current phase of the event loop.

## `process.nextTick()`  vs  `setImmediate()`

We have two calls that are similar as far as users are concerned, but their names are confusing.

-   `process.nextTick()`  fires immediately on the same phase
-   `setImmediate()`  fires on the following iteration or 'tick' of the event loop

In essence, the names should be swapped. process.nextTick() fires more immediately than setImmediate()

## Why use  `process.nextTick()`?

There are two main reasons:

1.  Allow users to handle errors, cleanup any then unneeded resources, or perhaps try the request again before the event loop continues.
    
2.  At times it's necessary to allow a callback to run after the call stack has unwound but before the event loop continues.
