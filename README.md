[![Build Status](https://travis-ci.com/jehy/runtime-coverage.svg?branch=master)](https://travis-ci.com/jehy/runtime-coverage) [![npm version](https://badge.fury.io/js/runtime-coverage.svg)](https://badge.fury.io/js/runtime-coverage)
[![dependencies Status](https://david-dm.org/jehy/runtime-coverage/status.svg)](https://david-dm.org/jehy/runtime-coverage)
[![devDependencies Status](https://david-dm.org/jehy/runtime-coverage/dev-status.svg)](https://david-dm.org/jehy/runtime-coverage?type=dev)
[![Known Vulnerabilities](https://snyk.io/test/github/jehy/runtime-coverage/badge.svg)](https://snyk.io/test/github/jehy/runtime-coverage)
[![Coverage Status](https://coveralls.io/repos/github/jehy/runtime-coverage/badge.svg?branch=master)](https://coveralls.io/github/jehy/runtime-coverage?branch=master)

# Runtime coverage

Enable coverage any time after service startup, gather coverage and disable it!

Useful for integration tests and checking for dead code branches.

## Usage

### Install

```bash

npm install runtime-coverage

```

### Start coverage

```js

const runtimeCoverage = require('runtime-coverage');
await runtimeCoverage.startCoverage();

```

### Gather and output coverage

```js

const options = {
  reporters: ['text'],
  return: true,
  all: true,
  exclude: ['**/node_modules/**'],
  };
const res = await runtimeCoverage.getCoverage(options);
console.log(res.text);

```

You can also get coverage in any format of istanbul reporters, for example cobertura.

### Options

Options for `getCoverage`:

 * `{Object} options` options for getting coverage
 * `{Array} [options.exclude]` exclude those files in coverage, [micromatch](https://github.com/micromatch/micromatch) array, default `**/node_modules/**`
 * `{string} [options.rootDir]` starting directory for files that need coverage info, default `process.env.PWD`
 * `{boolean} [options.all]` include files which were not used in coverage data, default `false`
 * `{boolean} [options.deleteCoverage]` delete coverage directory after output, default `true`
 * `{string} [options.coverageDirectory]` Directory for storing coverage, defaults to temporary directory
 * `{boolean} [options.return]` return coverage data, default `true`.
 * `{boolean} [options.stream]` return coverage data as a stream, useful for huge coverage data,  default `false`
 * `{boolean} [options.streamTimeout]` destroy stream if not used after this timeout,  default `60*1000` millis
 * `{Array} [options.reporters]` Array of reporters to use, default "text", see all possible [here](https://github.com/istanbuljs/istanbuljs/tree/master/packages/istanbul-reports/lib).

if `return` option is true, result will be an object with coverage data, keys are reporters's
names. For example, for `text` reporter result will be in `res.text`, for `cobertura` it
will be `res.cobertura` and so on.

if `stream` option is true, object will have same structure, but instead of data there
will be a stream, which will be destroyed after `streamTimeout` if not used.

### Sample

Short express sample [here](https://github.com/jehy/runtime-coverage-sample).

### Beware

After first module call, in subsequent calls V8 only reports partial coverage for called
functions, and it leads to 100% module coverage. To avoid it, either:
* Call all your code after `startCoverage()` call
* Use option `forceLineMode` -  it can only help with line coverage and is approximate because
can't know about comments, empty lines and statements - but it's safe
* Use option `forceReload` (used by default) - it will try to safely reload and run your file. But
thinks may break, kitten may die, and devil may be summoned - I warned you!

V8 coverage is still experimental, so of cause some thing are pretty clumsy.
I'm still hoping to make a perfect runtime coverage - may be with help of istanbul metadata. Wanna help?

### Debug

If you wanna debug library work, launch your project with env variable
`DEBUG=runtime-coverage:* `
