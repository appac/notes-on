# Notes On: Arrow Functions

Arrow functions are a shorter syntax means of writing functions in JavaScript. They *aren't* however a like for like replacement of regular functions for a couple of reasons:

- They do not have a `this`, `arguments`, `super`, or `new.target`.
- They cannot be used as constructors.

This is why you'll see it stated (on the [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) for example) that arrow functions aren't suited for method functions. Because an arrow function does not define its own `this` value, it inherits from the enclosing context which can lead to situations where `this` refers to the global (or some other) context instead of what you'd like it to.

This can sound like a limitation, but a shorter syntax means of writing functions with lexically capture their `this` value can be convenient. The key is understanding how arrow functions work, and where they're not suitable for use.

In the example below, using an arrow function as a prototype method leaves us with `undefined`, because there is no `this.data` on the context the arrow function inherited. For Node.js, this context would be `global`, and in browser it would be `Window`.

```javascript
function DataTransformer(data) {
  this.data = data;
}

DataTransformer.prototype.transform = () => {
  console.log(this.data);
};

const dt = new DataTransformer({
  some: 'data'
});

dt.transform(); // undefined
```

If however the `transform()` function is defined within DataTransformer, it would work with an arrow function, as the arrow function would inherit `this` from the surrounding function (DataTransformer).

```javascript
function DataTransformer(data) {
  this.data = data;
  this.transform = () => {
    console.log(this.data);
  }
}

const dt = new DataTransformer({
  some: 'data'
});

dt.transform(); // { some: 'data' }
```

## Implicit Return

Arrow functions can, through implicitly returning a value, substantially shorten the function syntax. When an arrow functions body is not a block, but an expression, the result of that expression is implicitly returned.

*Mapping an array of objects using regular function syntax.*

```javascript
const players = [
  {
    name: 'Phil McHomerun',
    age: 34
  },
  {
    name: 'Juan Strike',
    age: 27
  },
];

const ages = players.map(function (player) {
  return player.age;
});

console.log(ages);
```

*The same done using arrow function implicit return syntax.*

Notice too, the lack of parenthesis around the `player` parameter. If there is only a single parameter to work with, no parnthesis are required.

```javascript
const players = [
  {
    name: 'Phil McHomerun',
    age: 34
  },
  {
    name: 'Juan Strike',
    age: 27
  },
];

const ages = players.map((player => player.age);

console.log(ages);
```

## Sources/Further Reading

- [Exploring JS](http://exploringjs.com/es6/ch_arrow-functions.html)
- [MDN - Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [When Not to Use an Arrow Function](http://wesbos.com/arrow-function-no-no/)
