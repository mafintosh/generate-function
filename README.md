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
    function add (n) {')
      return n + ${d(val)}) // supports format strings to insert values
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

  // use gen.toString() if you want to see the generated source

  return gen.toFunction({multiply})
}

const addAndMultiply2 = addAndMultiplyNumber(2)

console.log(addAndMultiply2.toString())
console.log('(3 + 2) * 2 =', addAndMultiply2(3))
```

You can call `gen(src)` as many times as you want to append more source code to the function.

If you need a unique identifier for the scope of the generated function call `gen.sym('friendlyName')`.

## Related

See [generate-object-property](https://github.com/mafintosh/generate-object-property) if you need to safely generate code that
can be used to reference an object property

## License

MIT
