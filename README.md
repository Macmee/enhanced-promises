[![Build Status](https://travis-ci.org/Macmee/enhanced-promises.svg?branch=master)](https://travis-ci.org/Macmee/enhanced-promises)

## What is this?

As you might know, Javascript Harmony introduces native Promises. However, these Promises often lack useful functionality we are used to with other promise libraries such as Q. This library very simply adds useful methods to native Javascript promises such as `spread`, `delay`, `fcall`, `all()`, `nbind`, `ninvoke`, `nfcall`, `nfapply`, `denodify`, `nbind` and `npost`.

Additionally, if native promises are not available, the library includes a very simple Promise implementation as a substitute.

## What's the point?

I wanted to use native JS Promises, I wanted `spread` and `delay` from Q, and I didn't want to special case old browsers / versions of node that don't support promises.

## How do I use this?

`npm install enhanced-promises` and include `require('enhanced-promises')` and carry on using `new Promise(...)` as you would before, with the added methods sprinked on top. If you're in a browser then include `enhanced-promises.min.js`

*mostly stolen from Q documentation since it's the same*

### Creating promises

## General Promises

So as I said, this is a Promise library. Ideally the environment already supports promises, but if not this library provides a fallback. (from firefox documentation): The Promise object is used for deferred and asynchronous computations. A Promise represents an operation that hasn't completed yet, but is expected in the future. Learn more about promises, see more about promises at https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise

You can create promises in numerous ways, a few examples include:

```
new Promise(function(resolve, reject) {
  setTimeout(function(){ resolve(123) }, 1000);
}).then(function(value) {
  console.log(value); //123
});
```

```
Promise.fcall(function() {
  return 123;
}).then(function(value) {
  console.log(value); //123
});
```

```
Promise.resolve(123).then(function(value) {
  console.log(value); //123
});
```

```
Promise.reject(123).catch(function(value) {
  console.log(value); //123
});
```

## enhancement: Promise.delay(ms)

returns a promise that simply delays for X ms and then resolves

```
Promise
  .delay(1000)
  .then(function() {
    console.log('this prints after 1 second');
  })
```

## enhancement: Promise.fcall

fcall

You can create a promise from a value using Q.fcall. This returns a promise for 10.

return Q.fcall(function () {
    return 10;
});
You can also use fcall to get a promise for an exception.

return Q.fcall(function () {
    throw new Error("Can't do it");
});

## Defer

You can use deferred objects like so:

```
var deferred = Promise.defer();
FS.readFile("foo.txt", "utf-8", function (error, text) {
    if (error) {
        deferred.reject(new Error(error));
    } else {
        deferred.resolve(text);
    }
});
return deferred.promise;
```

## enhancement: Adapting NodeJS

You can wrap promises around nodejs functionality:

```
Promise.nfcall(FS.readFile, "foo.txt", "utf-8").then(function(text){ ... });
Promise.nfapply(FS.readFile, ["foo.txt", "utf-8"]).then(function(text){ ... });
```

If you want to invoke a function belonging to an object without changing the value for `this` you can do:

```
Promise.ninvoke(redisClient, "get", "user:1:id");
Promise.npost(redisClient, "get", ["user:1:id"]);
```

and you can make re-usable versions of the above like so:

```
var readFile = Q.denodeify(FS.readFile);
return readFile("foo.txt", "utf-8");

var redisClientGet = Q.nbind(redisClient.get, redisClient);
return redisClientGet("user:1:id");
```

### things you can do to promise objects

## enhancement: Spread

use as a replacement for then, if the previous promise returned an array, spread treats each element as a separate property, for example:

```
var getUsername = function() { .. returns some random promise .. }

Promise
  .all([
    123,
    Promise.resolve(456)
    getUsername()
  ])
  .spread(function(firstNumber, secondNumber, username) {
    console.log('woohoo!');
  })
```
