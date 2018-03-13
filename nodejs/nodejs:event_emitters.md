# Notes On: Event Emitters

Many of Node.js native modules make use of event emitters to provide human readable event driven applications. By human readable (or idiomatic) we mean that an event is denoted by a string which can clearly describe what the event is. This is possible using a regular callback and Error object (by augmenting the Error's porperties), but Events give us the added benefit of not being limited to one callback. We can account for any number of events that might arise in some function.

For example, the native `http`/`https` module emits numerous events which make is easy to keep the state of a request.

```javascript
const https = require('https');

https.get('www.someurl.com', (res) => {
    // check res.statusCode etc.
    res.on('data', (dataChunk) => { // 'data' event
        // handle data chunk
    });
    res.on('end', () => { // 'end' event
        // request complete
    });
}).on('error', (err) => { // 'error' event
    // handle request error
});
```

## Creating Events

Any object that emits an event is an instance of `EventEmitter`. We can create our own event emitting objects using ES6 classes like so:

```javascript
const EventEmitter = require('events').EventEmitter;

class Person extends EventEmitter {
    constructor(name) {
      super();
      this.name = name;
    }

    sayHelloTo(user) {
        // emit a 'greeting' event with the greeting string
        this.emit('greeting', `Hello ${user}! I'm ${this.name}.`);
    }

    sayHelloAgain() {
      this.emit('greeting', `Hello again ${this.user}!`);
    }
}

const kate = new Person('Kate');

// Set up listener - this will fire when "Kate" emits the 'greeting' event.
kate.on('greeting', (response) => {
    console.log(response);
});

// Call method, emitting the 'greeting' event.
kate.sayHelloTo('Pete');
kate.sayHelloAgain();
```

## Sync/Async

Event listeners are called synchronously so that events don't get crossed up with one another. If you need async events, use `setImmediate()` within the listening function body.

```javascript
kate.on('greeting', (response) => {
  setImmediate(() => {
    console.log(response);
  });
});
```

## One Time

Using the `eventEmitter.on()` method will mean that you continue to listen for the event after it's been emitted. If you wanted to only listen for an event once, you would use the `eventEmitter.once()` method.

With the example below, even though we call `sayHelloAgain()` we wouldn't get a second greeting, as the listener is unregistered before being called.

```javascript
kate.once('greeting', (response) => {
  console.log(response);
});

kate.sayHelloTo('Pete');
kate.sayHelloAgain();
```

## Error Events

It's important (and a best practice) to handle errors properly using event emitters. That means having an `error` event emit on errors, and a registered listener listening for error events. If not such listener exists and an `error` event is emitted, Node.js will throw `ERR_UNHANDLED_ERROR` and exit i.e. crash.

Using the code example from above:

```javascript
const EventEmitter = require('events').EventEmitter;

class Person extends EventEmitter {
    constructor(name) {
      super();
      this.name = name;
    }

    sayHelloTo(user) {
        this.user = user;
        if (!this.user) {
            // emit an error event if no user is provided
            this.emit('error', new Error(`No user provided to sayHelloTo.`));
        } else {
            // emit a 'greeting' event with the greeting string
            this.emit('greeting', `Hello ${user}! I'm ${this.name}.`);
        }
    }

    sayHelloAgain() {
        if (!this.user) {
            this.emit('error', new Error(`No user provided to sayHelloTo.`));
        } else {
            this.emit('greeting', `Hello again ${this.user}!`);
        }
    }
}

const kate = new Person('Kate');

// Set up listener - this will fire when "Kate" emits the 'greeting' or 'error' event.
kate.on('greeting', (response) => {
    console.log(response);
}).on('error', (err) => {
    console.log(err.message);
});

kate.sayHelloTo();
kate.sayHelloAgain();
```

## Sources/Further Reading

- [Event Emitters - Joyent](https://www.joyent.com/node-js/production/design#EventEmitter)
- [Event Emitters - Node.js Documentation](https://nodejs.org/api/events.html)
