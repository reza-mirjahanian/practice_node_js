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
