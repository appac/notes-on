# Notes On: Error Handling

Handling errors in Node.js can take a variety of valid forms, largely because the types of errors encountered can take a variety of forms. Thinking of errors as coming from one of two broad groups can help provide some context on how to handle them.

## Operational Errors

These are run time errors that aren't the result of a bug or some poorly written code, so can be properly handled. Operational errors are an expected and normal part of an application.

- Run time errors
  - Bad user input
  - request timeout
  - connection errors
  - server 500

### Handling Operational Errors

**Resolve it directly**  - If the error is caused by something you can assume to be a temporary issue, you could attempt to resolve the problem directly when the error occurs. Say some file that should exist, doesn't because it's a first run. When that error crops up, it's easily resolved by creating the required file.

**Propagate** - Passing the error up the chain to the user is another option. If the error is likely to be caused by a more permanent issue, say the user providing invalid input, without propagating the error and letting the user know there's an issue, they'll keep trying their bad input.

**Retry** - For remote operations you could perform some number of retries, letting the user know how many times you'll retry before failing. If the error is occuring deep in the stack, fail fast and let the user handle retries.

**Fail** - If the error is a result of some issue that cannot be readily resolved, report the error to the user, and crash out.

**Log** - Let the user know about the error, before logging it and continuing execution.

## Programmer Errors

These are errors that can't be properly handled, since they are the result of an error in the application code itself.

- Trying to access a variable that does not exist
- mishandling callbacks
- passing incorrect type
- unhandled error event emitted

### Handling Programmer Errors

You dont. A programmer error is due some case you hadn't considered and once one occurs you cannot safely assume anything about the state of your application. Crashing immediately is the best option, allowing your restarter/process manager to handle restarting your application -- PM2 does this by default, for example.

### Determining Operational and Programmer Errors

Any functions your write should have clear documentation and definitions on what types they accept as arguments. In doing this, you are better able to determine whether an error is a programmer error (a bug) or an operational one (bad user input).

## throw vs callbacks/event emitters

`throw` is synchronous error handling, whilst callbacks and event emitters are asynchronous. So deciding which to use it going to depend on whether the function you're in is synchronous or asynchronous.

If it's asynchronous, a an error first callback is a good option and the most common. In the example below, `randomError` takes a parameter `done`, which is a function that itself takes `err` and `data` parameters. When calling `randomError` we can then pass it a callback function, where we check if `err` is non-null, and if it is not, we log that error to console or do some other handling.

```javascript
function randomError(done) {
  const errorHasOccured = Math.floor(Math.random() * 2);
  if (errorHasOccured === 1) {
    const err = new Error(`Uh oh, an error occured!`);
    return done(err, null);
  }
  const data = {
    some: 'data'
  };
  return done(null, data);
}

someFunction((err, data) => {
  if (err) {
    return console.log(err.message);
  }
  return console.log(data);
});
```

An event emitter is also an option if your function is particularly complex and moves through different states whilst performing an operation, for example, the Node `http`/`https`.

In general, a function should handle errors in an async way (callbacks/events) *or* a sync way (throwing/returning), but not both.

## Sources/Further Reading

- [Error Handling - Joyent](https://www.joyent.com/node-js/production/design/errors)
