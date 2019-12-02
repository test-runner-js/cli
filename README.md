[![view on npm](https://img.shields.io/npm/v/test-runner.svg)](https://www.npmjs.org/package/test-runner)
[![npm module downloads](https://img.shields.io/npm/dt/test-runner.svg)](https://www.npmjs.org/package/test-runner)
[![Build Status](https://travis-ci.org/test-runner-js/test-runner.svg?branch=master)](https://travis-ci.org/test-runner-js/test-runner)
[![Dependency Status](https://badgen.net/david/dep/test-runner-js/test-runner)](https://david-dm.org/test-runner-js/test-runner)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](https://github.com/feross/standard)

***This project and documentation are a WIP***

# test-runner

Lightweight command-line test runner. Part of a suite of tools aiming to help the full-stack JavaScript engineer create reusable, isomorphic code which runs in both Node.js and the browser.

## Synopsis

As input, test-runner takes one or more files each exporting a set of tests. This is the general syntax (see [here](https://github.com/test-runner-js/test-runner/wiki/test-runner-command-line-options) for the full usage guide):

```
$ test-runner [<options>] <file> ...
```

A test file is a module which exports a [test object model](https://github.com/test-runner-js/test-object-model) instance. Add tests to the model by invoking [`tom.test`](https://github.com/test-runner-js/test-object-model/blob/master/docs/API.md#module_test-object-model--Tom+test) with a name and test function. Once a test file is written, it can be run in Node.js using [test-runner](https://github.com/test-runner-js/test-runner), [esm-runner](https://github.com/test-runner-js/esm-runner) or [mc-runner](https://github.com/test-runner-js/mc-runner) or in the browser (Chromium) using [web-runner](https://github.com/test-runner-js/web-runner).

Trivial test file example. If the test function throws or returns a rejected promise it is considered a fail.

```js
const { Tom } = require('test-runner')

const tom = new Tom()

tom.test('A successful test', function () {
  return 'This passed'
})

tom.test('A failing test', function () {
  throw new Error('This failed')
})

module.exports = tom
```

### Test CommonJS JavaScript using Node.js

In reality, a typical test suite might look more like this. Save this as `test.js`.

```js
const { Tom } = require('test-runner')
const assert = require('assert').strict
const fetch = require('node-fetch')

const tom = new Tom()

tom.test('Math.random() should return a number between 0 and 1', function () {
  const result = Math.random()
  assert.equal(typeof result, 'number')
  assert.ok(result >= 0 && result <= 1)
})

tom.test('REST API should return the current todo item', async function () {
  const response = await fetch('https://jsonplaceholder.typicode.com/todos/1')
  const todo = await response.json()
  assert.equal(todo.userId, 1)
  assert.equal(todo.title, 'delectus aut autem')
})

module.exports = tom
```

Run the tests using test-runner.

```
$ npx test-runner test.js

Start: 2 tests loaded

✓ synopsis Math.random() should return a number between 0 and 1
✓ synopsis REST API should return the current todo item

Completed in 199ms. Pass: 2, fail: 0, skip: 0.
```

### Test ECMAScript Module JavaScript using Node.js

If you're working with ES Modules, your test file will look more like this (use `import` and `export default` instead of `require` and `module.exports`).

```js
import TestRunner from 'test-runner'
import assert from 'assert'
import fetch from 'node-fetch'

const tom = new TestRunner.Tom()

tom.test('Math.random() should return a number between 0 and 1', function () {
  const result = Math.random()
  assert.equal(typeof result, 'number')
  assert.ok(result >= 0 && result <= 1)
})

tom.test('REST API should return the current todo item', async function () {
  const response = await fetch('https://jsonplaceholder.typicode.com/todos/1')
  const todo = await response.json()
  assert.equal(todo.userId, 1)
  assert.equal(todo.title, 'delectus aut autem')
})

export default tom
```

To run these tests you would need [esm-runner](https://github.com/test-runner-js/esm-runner). This runner is identical to test-runner with the one difference that it supports ECMAScript Modules (ESM).

```
$ npx esm-runner test.mjs

Start: 2 tests loaded

✓ synopsis Math.random() should return a number between 0 and 1
✓ synopsis REST API should return the current todo item

Completed in 199ms. Pass: 2, fail: 0, skip: 0.
```

### Test ECMAScript Module JavaScript in the browser using Chromium

To run our test file in the browser we need to make a couple of adjustments. Instead of Node's `assert` we use chai. Also, there is no need to load `node-fetch` since `fetch` is built into the browser.


```js
import Tom from 'test-object-model'
import 'https://www.chaijs.com/chai.js'

const assert = chai.assert
const tom = new Tom()

tom.test('Math.random() should return a number between 0 and 1', function () {
  const result = Math.random()
  assert.equal(typeof result, 'number')
  assert.ok(result >= 0 && result <= 1)
})

tom.test('REST API should return the current todo item', async function () {
  const response = await fetch('https://jsonplaceholder.typicode.com/todos/1')
  const todo = await response.json()
  assert.equal(todo.userId, 1)
  assert.equal(todo.title, 'delectus aut autem')
})

export default tom
````

Run the tests in Chromium using web-runner.

```
$ web-runner browser.mjs

Start: 2 tests loaded

✓ browser Math.random() should return a number between 0 and 1 3.7ms
✓ browser REST API should return the current todo item 122.0ms

Completed in 134ms. Pass: 2, fail: 0, skip: 0.
```

Please see [the wiki](https://github.com/test-runner-js/test-runner/wiki) for more examples.

## Install

```
$ npm install --save-dev test-runner
```

## Test-runner tool kit

Alternatively, you can run your tests with any of the following runners - each is compatible with test-object-model.

| Environment  | Description                          | Tool          |
| -----------  | ------------------------             | ------------- |
| Web          | Run your tests in headless Chrome from the command line | [web-runner](https://github.com/test-runner-js/web-runner)    |
| Multi-core   | Run a test suite across multiple CPU cores | [mc-runner](https://github.com/test-runner-js/mc-runner) |
| ECMAScript Modules | Test an Node.js ESM project natively without transpilation | [esm-runner](https://github.com/test-runner-js/esm-runner) |
| Script       | Programmatic | [test-runner-core](https://github.com/test-runner-js/test-runner-core) |


* * *

&copy; 2016-20 Lloyd Brookes \<75pound@gmail.com\>.
