# [gulp](https://github.com/gulpjs/gulp)-watch [![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Coverage Status][coveralls-image]][coveralls-url] [![Dependency Status][depstat-image]][depstat-url]
> Watch, that actually is an endless stream

###  

This is an __reimplementation__ of bundled [`gulp.watch`](https://github.com/gulpjs/gulp/blob/master/docs/API.md#gulpwatchglob—opts-cb) with an endless-stream approach. If `gulp.watch` is working for you, stick with it; otherwise, you can try this `gulp-watch` plugin.

The main reason for `gulp-watch`'s existence is that it can easily achieve per-file rebuilding on file change:

![Awesome demonstration](https://github.com/floatdrop/gulp-watch/raw/master/img/2014-01-09.gif)

## Installation

Run `npm install gulp-watch`.

## Usage

```js
var gulp = require('gulp'),
    watch = require('gulp-watch');

gulp.task('default', function () {
    gulp.src('css/**/*.css')
        .pipe(watch('css/**/*.css'))
        .pipe(gulp.dest('./build/'));
});
```

> __Protip:__ until gulpjs 4.0 is released, you can use [gulp-plumber](https://github.com/floatdrop/gulp-plumber) to prevent stops on errors.

More examples can be found in [`docs/readme.md`](/docs/readme.md).

## API

### watch(glob, [options, callback])

Creates watcher that will spy on files that were matched by `glob` which can be a
[`node-glob`](https://github.com/isaacs/node-glob) string or array of strings.

Returns pass through stream, that will emit vinyl files
(with additional `event` property) that corresponds to event on file-system.

#### Callback `function(vinly)`

This function is called, when some events is happens on file-system.
All incoming files that piped in will be grouped and passed to `events` stream as is.

 * `vinyl` — is [vinyl](https://github.com/wearefractal/vinyl) object that corresponds to file that caused event. Additional `event` field is added to determine, what caused changes.

#### Options

This object is passed to [`gaze` options](https://github.com/shama/gaze#properties) directly (refer to [`gaze` documentation](https://github.com/shama/gaze)). Options for [`gulp.src`](https://github.com/gulpjs/gulp#gulpsrcglobs-options) are used. If you do not want content from `watch`, then add `read: false` to the `options` object.

#### options.base
Type: `String`  
Default: `undefined`

Use explicit base path for files from `glob`.

#### options.name
Type: `String`  
Default: `undefined`

Name of the watcher. If it present in options, you will get more readable output:

![Naming watchers](https://github.com/floatdrop/gulp-watch/raw/master/img/naming.png)

#### options.verbose
Type: `Boolean` / `undefined`  
Default: `undefined`

This options will enable more verbose output (on `true`) or disable it completly (on `false`).

### Methods

Returned `Stream` from constructor have some useful methods:

 * `close()` — calling `gaze.close` and emitting `end`, after `gaze.close` is done.

Also it has `_gaze` property to access Gaze instance.

### Events

 * `end` — all files are stop being watched.
 * `ready` — just re-emitted event from `gaze`.
 * `error` — when something happened inside callback, you will get notified.

### 3.0.0 Changes

[gulp-batch](https://github.com/floatdrop/gulp-batch) was removed, so `callback` is changed. Now it passes just [vinyl](https://github.com/wearefractal/vinyl) object to you. Also `callback` now not catching errors inside and reemits them to stream.

### 2.0.0 Changes

Before `2.0.0` version there was a bug in `gulp-batch` - it does not prevent tasks to execute in same time. In `2.0.0` version of `gulp-batch` was bumped.

This can cause your watch tasks to hang, if you do not calling `done` in callback or returning `Stream`/`Promise` from it.

### Migration to 1.0.0

 * __watch is not emmiting files at start__ - read «[Starting tasks on events](/docs/readme.md#starting-tasks-on-events)» and «[Incremental build](https://github.com/floatdrop/gulp-watch/tree/master/docs#incremental-build)» for workarounds.
 * __watch is now pass through stream__ - which means that streaming files into watch will not add them to gaze. It is very hard to maintain, because watch is not aware about `glob`, from which this files come from and can not re-create vinyl object properly without maintaining cache of the `base` properties of incoming files (yuck).
 * __array of tasks is not accepted as callback__ - this was not working anyway, but rationale behind it - requiring gulp and calling internal method start is bad. This feature will become more clear, when gulp 4.0.0 will be released with new task system. Read «[Starting tasks on events](/docs/readme.md#starting-tasks-on-events)» for right way to do it.

# License

MIT (c) 2014 Vsevolod Strukchinsky (floatdrop@gmail.com)

[npm-url]: https://npmjs.org/package/gulp-watch
[npm-image]: http://img.shields.io/npm/v/gulp-watch.svg?style=flat

[travis-url]: https://travis-ci.org/floatdrop/gulp-watch
[travis-image]: http://img.shields.io/travis/floatdrop/gulp-watch.svg?style=flat

[coveralls-url]: https://coveralls.io/r/floatdrop/gulp-watch
[coveralls-image]: http://img.shields.io/coveralls/floatdrop/gulp-watch.svg?style=flat

[depstat-url]: https://david-dm.org/floatdrop/gulp-watch
[depstat-image]: http://img.shields.io/david/floatdrop/gulp-watch.svg?style=flat
