# base-task [![NPM version](https://img.shields.io/npm/v/base-task.svg?style=flat)](https://www.npmjs.com/package/base-task) [![NPM monthly downloads](https://img.shields.io/npm/dm/base-task.svg?style=flat)](https://npmjs.org/package/base-task) [![NPM total downloads](https://img.shields.io/npm/dt/base-task.svg?style=flat)](https://npmjs.org/package/base-task) [![Linux Build Status](https://img.shields.io/travis/node-base/base-task.svg?style=flat&label=Travis)](https://travis-ci.org/node-base/base-task)

> base plugin that provides a very thin wrapper around [https://github.com/doowb/composer](https://github.com/doowb/composer) for adding task methods to your application.

You might also be interested in [base-watch](https://github.com/node-base/base-watch).

## Install

Install with [npm](https://www.npmjs.com/):

```sh
$ npm install --save base-task
```

## Usage

```js
var Base = require('base');
var tasks = require('base-task');

var base = new Base();
// Since some applications have multiple types of plugins, you must 
// set "isApp" on the instance you want base-task to run on 
base.isApp = true;
base.use(tasks());

/**
 * Define tasks
 */

base.task('foo', function(cb) {
  console.log('this is foo!');
  cb();
});
base.task('bar', function(cb) {
  console.log('this is bar!');
  cb();
});

/**
 * Build tasks
 */

base.build(['foo', 'bar'], function(err) {
  // this is foo!
  // this is bar!
  console.log('done!');
});
```

See the [composer](https://github.com/jonschlinkert/composer) documentation for more details, or to create bug reports related to running or registering tasks.

## API

### .task

Register a task

**Params**

* `name` **{String}**: Task name to register (tasks are cached on `app.tasks`)
* `dependencies` **{String|Array|Function}**: String, list or array of tasks.
* `callback` **{Function}**: Function to be called when the task is executed. Task functions should either return a stream or call the callback to let [composer](https://github.com/doowb/composer) know when the task is finished.

**Examples**

Register a task.

```js
app.task('default', function() {
  // return the stream to signal "done"
  return app.src('pages/*.hbs')
    .pipe(app.dest('dist'));
});
```

Register a task with dependencies (other tasks to run before executing the task):

```js
app.task('site', ['styles'], function() {
  return app.src('pages/*.hbs')
    .pipe(app.dest('dist'));
});

app.task('default', ['site']);
```

**Get a task**

```js
var task = app.task('site');
```

### .build

Run a task or array of tasks.

**Example**

```js
app.build('default', function(err, results) {
  if (err) {
    console.error(err);
    return;
  }
  console.log(results);
});
```

### .series

Compose task or list of tasks into a single function that runs the tasks in series.

**Params**

* `tasks` **{String|Array|Function}**: List of tasks by name, function, or array of names/functions.
* `returns` **{Function}**: Composed function that may take a callback function.

**Example**

```js
app.task('foo', function(cb) {
  console.log('this is foo');
  cb();
});

var fn = app.series('foo', function(cb) {
  console.log('this is bar');
  cb();
});

fn(function(err) {
  if (err) return console.error(err);
  console.log('finished');
});
//=> this is foo
//=> this is bar
//=> finished
```

### .parallel

Compose task or list of tasks into a single function that runs the tasks in parallel.

**Params**

* `tasks` **{String|Array|Function}**: List of tasks by name, function, or array of names/functions.
* `returns` **{Function}**: Composed function that may take a callback function.

**Example**

```js
app.task('foo', function(cb) {
  setTimeout(function() {
    console.log('this is foo');
    cb();
  }, 500);
});

var fn = app.parallel('foo', function(cb) {
  console.log('this is bar');
  cb();
});

fn(function(err) {
  if (err) return console.error(err);
  console.log('finished');
});
//=> this is bar
//=> this is foo
//=> finished
```

## Events

The following events are emitted by [composer](https://github.com/jonschlinkert/composer). See the composer docs for more details

### on.task

Emitted when a `task` is `register`, `starting` and `finished`.

```js
app.on('task', function(task) {
  console.log(task.status);
  //=> 'register'
});
```

### on.build

Emitted when a `build` is `starting` and `finished`.

```js
app.on('build', function(build) {
  console.log(build.status);
  //=> 'starting'
});
```

## History

**v0.3.0**

* Bumped [composer](https://github.com/doowb/composer) to v0.11.0, so the `.watch` method is no longer included by default. To add `.watch`, use the [base-watch](https://github.com/node-base/base-watch) plugin.

## About

### Related projects

* [base-cli](https://www.npmjs.com/package/base-cli): Plugin for base-methods that maps built-in methods to CLI args (also supports methods from a… [more](https://github.com/node-base/base-cli) | [homepage](https://github.com/node-base/base-cli "Plugin for base-methods that maps built-in methods to CLI args (also supports methods from a few plugins, like 'base-store', 'base-options' and 'base-data'.")
* [base-generators](https://www.npmjs.com/package/base-generators): Adds project-generator support to your `base` application. | [homepage](https://github.com/node-base/base-generators "Adds project-generator support to your `base` application.")
* [base-option](https://www.npmjs.com/package/base-option): Adds a few options methods to base, like `option`, `enable` and `disable`. See the readme… [more](https://github.com/node-base/base-option) | [homepage](https://github.com/node-base/base-option "Adds a few options methods to base, like `option`, `enable` and `disable`. See the readme for the full API.")
* [base-plugins](https://www.npmjs.com/package/base-plugins): Adds 'smart plugin' support to your base application. | [homepage](https://github.com/node-base/base-plugins "Adds 'smart plugin' support to your base application.")
* [base-store](https://www.npmjs.com/package/base-store): Plugin for getting and persisting config values with your base-methods application. Adds a 'store' object… [more](https://github.com/node-base/base-store) | [homepage](https://github.com/node-base/base-store "Plugin for getting and persisting config values with your base-methods application. Adds a 'store' object that exposes all of the methods from the data-store library. Also now supports sub-stores!")
* [base](https://www.npmjs.com/package/base): Framework for rapidly creating high quality node.js applications, using plugins like building blocks | [homepage](https://github.com/node-base/base "Framework for rapidly creating high quality node.js applications, using plugins like building blocks")

### Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](../../issues/new).

### Contributors

| **Commits** | **Contributor** | 
| --- | --- |
| 50 | [jonschlinkert](https://github.com/jonschlinkert) |
| 6 | [doowb](https://github.com/doowb) |
| 2 | [davequick](https://github.com/davequick) |

### Building docs

_(This project's readme.md is generated by [verb](https://github.com/verbose/verb-generate-readme), please don't edit the readme directly. Any changes to the readme must be made in the [.verb.md](.verb.md) readme template.)_

To generate the readme, run the following command:

```sh
$ npm install -g verbose/verb#dev verb-generate-readme && verb
```

### Running tests

Running and reviewing unit tests is a great way to get familiarized with a library and its API. You can install dependencies and run tests with the following command:

```sh
$ npm install && npm test
```

### Author

**Jon Schlinkert**

* [github/jonschlinkert](https://github.com/jonschlinkert)
* [twitter/jonschlinkert](https://twitter.com/jonschlinkert)

### License

Copyright © 2017, [Jon Schlinkert](https://github.com/jonschlinkert).
Released under the [MIT License](LICENSE).

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.6.0, on July 17, 2017._