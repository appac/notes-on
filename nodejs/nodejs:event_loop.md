# Notes On: Node.js Event Loop

The Node.js Event Loop is made up of a series of phases which are executed in a specific order as Node.js is performing some operation (executing your code). Moving from one phase to the next can be referred to as a "tick", and each phase is specialised in that it will only handle specific operations.

When you perform some operation in Node, a "reference counter" is iterated by one. As the loop comes to an end and completes its close events (the bottom of the loop below), if that counter is still greater than zero the loop is continued. Otherwise, it exits.

![node.js event loop diagram][nodejs_event_loop_diag]

## Phases

### timers

Executes callbacks scheduled by `setTimeout()` and `setInterval()`.

Since other operations can delay the execution of a timers callback, a timer only dictates the minimum amount of time *after* with a callback can be executed, rather than the exact time at which it will execute.

This means that if you `setTimeout()` for *n* ms, the event loop would wait at least *n* ms before executing the callback. However, if in waiting some other operation had some immediate callback to be dealt with that took, say, 15ms, the total time before the timeout callback would be executed would be at least *n*+15.



### I/O Callbacks

This phase executes callbacks for system operations.

### poll

In this phase queued callbacks are executed, but only up to some system dependant hard limit. Since the queue execution is synchronous, the hard limit is a requirement to stop the poll phase from bringing the entire event loop to an end.

If/when the queue is empty, one of two things can happen:

- If there are any calls to `setImmediate()`, the poll phase is ended and the event loop moves to the check phase to execute those.
- Otherwise, the event loop will wait on the poll phase for callbacks to be added to the queue, executing them immediately.

It should be noted that as mentioned earlier, the timers phase doesn't dictate when some callback is executed. That actually occurs here, where poll will determine what timers have callbacks ready to be executed, to be so on the next timers phase.

### check

This is where the `setImmediate()` callbacks are executed as noted above.

### close callabcks

This phase handles "close events" for sockets or handles that were close abruplty. 

Usually, these events are handled by `process.nextTick()`, which occures between every phase (or tick). When moving between phases, a `nextTickQueue` is processed, with any callbacks/events handled before the event loop can continue to the next phase.

## Sources/Further Reading

- [Design Overview - libuv](http://docs.libuv.org/en/v1.x/design.html)
- [The Node.js Event Loop, Timers, and process.nextTick()](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
- [Node.js Event Loop - Bart Belder (video)](https://www.youtube.com/watch?v=PNa9OMajw9w&t=525s)

[nodejs_event_loop_diag]: ../images/node_event_loop_phases.jpg "Node.js Event Loop"
