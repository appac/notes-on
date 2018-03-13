# Notes On: `require`, `exports`

A key part of building applications with Node is taking advantage of modules. Breaking up your code into individual files which are easier to maintain and better express intent and design.

## Exporting

Assume we have some function that we want to make accessible throughout our Node.js application. It wouldn't be practical to copy-paste that function into every file that needs it. So we break the function out into its own file like so:

*addOne.js*

```javascript
function addOne(n) {
    return n + 1;
}
```

To turn this simple function into a module, we simply export it:

```javascript
function addOne(n) {
    return n + 1;
}

module.exports = addOne;
```

Now, we can import our function into any file that needs it.

## Importing

Importing a module uses the `require()` function, which allows you to pass in some file or module, storing it as a variable. As in:

*app.js*

```javascript
const addOne = require('./addOne.js');

addOne(5); // returns 6
```

## Uses

We can export almost anything, from functions, variables, to classes. Let's say we have a class we need to make available to other parts of our application, we could:

*Business.js*

```javascript
class Business {
    constructor(name) {
        this.name = name;
    }

    setEmployeeCount(count) {
        this.employeeCount = count;
    }

    getEmployeeCount() {
        return this.employeeCount;
    }
}

module.exports = Business;
```

*app.js*

```javascript
const Business = require('./Business');

const business = new Business('Coder Coffee');
business.setEmployeeCount('4');
business.getEmployeeCount(); // returns 4;
```

## Folders

Folders can be imported and exported also, allowing a lot of flexibility about how you structure your application.

This can be done by either adding a `package.json` file to the folder, and setting a `main` property pointing to the file to be loaded.

Alternatively, without a `package.json` file, Node will instead load `index.js` or `index.node` if either exist.

## Caching

The first time a module is imported using `require()`, it is cached. That means that every time that module is called after the first time, it's returning the same object.

## Sources/Further Reading

- [Modules - Node.js Documentation](https://nodejs.org/api/modules.html)
