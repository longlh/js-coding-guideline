Read more about [Google JavaScript Style Guide](https://google.github.io/styleguide/javascriptguide.xml)

# Declare `hash object`, `array`
 - Always use line-breaks
 - Always have `,` at end of each key-value pair/array element
 
```
var hash = {
	key1: value1,
	key2: value2,
};

var arr = [
	value1,
	value2,
];

functionCall({
	key1: value1,
	key2: value2,
}, [
	value3,
	value4,
]);
```

# Promise for async code: `bluebird` library

## Wrapping
```
new Bluebird(function(resolve, reject) {
	if (error) {
		return reject(error);
	}

	return resolve(value);
});
```

**Sugar methods**
```
Bluebird.resolve(value);

// equivalent to

new Bluebird(function(resolve, reject) {
	resolve(value);
});
```
```
Bluebird.reject(value);

// equivalent to

new Bluebird(function(resolve, reject) {
	reject(value);
});
```
## Promise serial
```
promise1.then(function(resolve1) {
	return promise2;
}).then(function(resolve2) {
	return resolve3;
}).then(function(resolve3) {
	// and so on
});
```
## Promise parallel
```
Bluebird.all([
	promise1,
	promise2,
	promise3,
]).then(function(resolves) {
	// resolves = [resolve1, resolve2, resolve3]
});
```
**Note** can use `.spread` 
```
Bluebird.all([
	promise1,
	promise2,
	promise3,
]).spread(function(resolve1, resolve2, resolve3) {

});
```
## Helper `async-object`

```
// old style
Bluebird.all([
	promise1,
	promise2,
	promise3,
]).spread(function(resolve1, resolve2, resolve3) {
	return {
		key1: resolve1,
		key2: resolve2,
		key3: resolve3,
	};
}).then(function(hash) {
	// use `hash` here
});

// new style
var asyncObject = require('async-object');

asyncObject({
	key1: promise1,
	key2: promise2,
	key3: promise3,
}).then(function(hash) {
	/*
		hash = {
			key1: resolve1,
			key2: resolve2,
			key3: resolve3,
		}
	*/
});
```

## `require` usage
 - In server-side, `require` is built-in with NodeJS
 - In client-side, `require` is provided by [Browserify](http://browserify.org/)
 - `require` is *synchronized*
 - group required dependencies by:
	+ third-party packages
	+ in-project packages
	+ alphabet A-Z
	+ objects/classes

```
var _ = require('underscore');
var asyncObject = require('async-object');
var request = require('request');

var Bluebird = require('bluebird');

var component = require('../services/component');
var template = require('../services/template');

var Group = require('../models/group');
var User = require('../models/user');
```