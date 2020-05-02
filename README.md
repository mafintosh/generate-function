# generate-function

Module that helps you write generated functions in Node

```
npm install generate-function
```

[![build status](http://img.shields.io/travis/mafintosh/generate-function.svg?style=flat)](http://travis-ci.org/mafintosh/generate-function)

## Disclamer

Writing code that generates code is hard.
You should only use this if you really, really, really need this for performance reasons (like schema validators / parsers etc).

## Usage

``` js
const genfun = require('generate-function')
const { d } = genfun.formats

function addNumber (val) {
  const gen = genfun()

  gen(`
    function add (n) {
      return n + ${d(val)} // supports format strings to insert values
    }
  `)

  return gen.toFunction() // will compile the function
}

const add2 = addNumber(2)

console.log('1 + 2 =', add2(1))
console.log(add2.toString()) // prints the generated function
```

If you need to close over variables in your generated function pass them to `toFunction(scope)`

``` js
function multiply (a, b) {
  return a * b
}

function addAndMultiplyNumber (val) {
  const gen = genfun()

  gen(`
    function (n) {
      if (typeof n !== 'number') {
        throw new Error('argument should be a number')
      }
      const result = multiply(${d(val)}, n + ${d(val)})
      return result
    }
  `)

  // use gen.toString() if you want to see the generated function as a string
  return gen.toFunction({multiply})
}

const addAndMultiply2 = addAndMultiplyNumber(2)

console.log(addAndMultiply2.toString())
console.log('(3 + 2) * 2 =', addAndMultiply2(3))
```

You can call `gen(src)` as many times as you want to append more source code to the function.

## Compiling A Module

If you need to compile a function as a standalone module, use `gen.toModule(scope)`. This method wraps the
scope into an [IIFE](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression) and returns it as a string. This is useful for compiling webpack modules or browser bundles.

```js
var gen = genfun(`
  function addXAndY() {
    return x + y
  }
`)

console.log(gen.toModule({ x: 9, y: 3 }))
/** output:
 *
 * (function() {
 * var x = 9;
 * var y = 3;;
 * return (function addXAndY() {
 *   return x + y
 * })})();
 */
```

## Variables

If you need a unique safe identifier for the scope of the generated function call `str = gen.sym('friendlyName')`.
These are safe to use for variable names etc.

## Object properties

If you need to access an object property use the `str = gen.property('objectName', 'propertyName')`.

This returns `'objectName.propertyName'` if `propertyName` is safe to use as a variable. Otherwise
it returns `objectName[propertyNameAsString]`.

If you only pass `gen.property('propertyName')` it will only return the `propertyName` part safely

## License

MIT
