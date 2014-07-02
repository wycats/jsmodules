---
title: Porting from CommonJS
---

Here are some example Node.js modules, and how they look in
JavaScript Next.

# CommonJS

# JavaScript Next

## body-parser.js

```js
var bytes = require("bytes");
var iconv = require("iconv-lite");

module.exports = function(stream, options, done) {
  // ...
}

// ...

function getDecoder(encoding) {
  if (!encoding) return null;

  try {
    return iconv.getCodec(encoding).decoder()
  } catch(e) {
    // ...
  }
}

// ...
```

```js
import bytes from "bytes";
import { getCodec } from "iconv-lite";

export default function(stream, options, done) {
  // ...
}

// ...

function getDecoder (encoding) {
  if (!encoding) return null;

  try {
    return getCodec(encoding).decoder()
  } catch(e) {
    // ...
  }
}

// ...
```

[full diff](https://gist.github.com/wycats/95e2cfdfefa98f006318/revisions)

## request.js

```js
var cookies = require('./lib/cookies')
  , copy = require('./lib/copy')
  , Request = require('./request')
  , util = require('util')
  ;

function initParams(uri, options, callback) {
  // ...
  opts = util._extend({}, options);
  // ...
}

// ...

function request (uri, options, callback) {
  // ...
  opts = util._extend({}, options);
  // ...
}

module.exports = request;

request.Request = Request;

request.debug = process.env.NODE_DEBUG &&
  /request/.test(process.env.NODE_DEBUG);

request.initParams = initParams

request.defaults = function (options, requester) {
  // ...

  de.get = def(request.get)
  de.patch = def(request.patch)
  de.post = def(request.post)
  de.put = def(request.put)
  de.head = def(request.head)
  de.del = def(request.del)
  de.cookie = def(request.cookie)
  de.jar = request.jar

  return de
}

// ...

request.forever = function (agentOptions, optionsArg) {
  // ...

  return request.defaults(options)
}

request.get = function (uri, options, callback) {
  // ...
}

request.post = function (uri, options, callback) {
  // ...
}

request.put = function (uri, options, callback) {
  // ...
}

request.patch = function (uri, options, callback) {
  // ...
}

request.head = function (uri, options, callback) {
  // ...
}

request.del = function (uri, options, callback) {
  // ...
}

request.jar = function (uri, options, callback) {
  // ...
}

request.cookie = function (uri, options, callback) {
  // ...
}
```

```js
import cookies from "./lib/cookies";
import copy from "./lib/copy";
import Request from "./lib/request";
import { _extend } from "util";


function initParams(uri, options, callback) {
  // ...
  opts = _extend({}, options);
  // ...
}

// ...

function request (uri, options, callback) {
  // ...
  opts = _extend({}, options);
  // ...
}

export default request;

export { Request };

export var debug = process.env.NODE_DEBUG &&
  /request/.test(process.env.NODE_DEBUG);

export var initParams = initParams

export function defaults (options, requester) {
  // ...

  de.get = def(get)
  de.patch = def(patch)
  de.post = def(post)
  de.put = def(put)
  de.head = def(head)
  de.del = def(del)
  de.cookie = def(cookie)
  de.jar = jar

  return de
}

// ...

export function forever (agentOptions, optionsArg) {
  // ...

  return defaults(options)
}

export function get (uri, options, callback) {
  // ...
}

export function post (uri, options, callback) {
  // ...
}

export function put (uri, options, callback) {
  // ...
}

export function patch (uri, options, callback) {
  // ...
}

export function head (uri, options, callback) {
  // ...
}

export function del (uri, options, callback) {
  // ...
}

export function jar (uri, options, callback) {
  // ...
}

export function cookie (uri, options, callback) {
  // ...
}
```

[full
diff](https://gist.github.com/wycats/4353447a213d3060e23f/revisions)

## json.js

```js
/**
 * Module dependencies
 */

var bytes = require('bytes')
var read = require('./read')
var typer = require('media-typer')
var typeis = require('type-is')

/**
 * Module exports
 */

module.exports = json

// ...

function json(options) {

}
```

```js
/**
 * Module dependencies
 */

import bytes from "bytes";
import read from "./read";
import typer from "media-typer";
import typeis from "type-is";

/**
 * Module exports
 */

export default json;

// ...

function json(options) {

}
```

[full
diff](https://gist.github.com/wycats/42dc6d94bcc57474cbef/revisions)

## mkdirp.js

```js
var path = require('path');
var fs = require('fs');


module.exports = mkdirP.mkdirp = mkdirP.mkdirP = mkdirP;

function mkdirP (p, opts, f, made) {
  // ...
  p = path.resolve(p);
  // ...
  mkdirP(path.dirname(p), opts, function (er, made) {
    // ...
  })
  // ...
}

// ...

mkdirP.sync = function sync (p, opts, made) {
  // ...
  p = path.resolve(p);
  // ...
  made = sync(path.dirname(p), opts, made);
  // ...
}
```

```js
import { resolve, dirname } from "path";
import * as fs from "fs";

/* aliases present in the original */
export { mkdirP as mkdirp, mkdirP };

export default function mkdirP (p, opts, f, made) {
  // ...
  p = resolve(p);
  // ...
  mkdirP(dirname(p), opts, function (er, made) {
    // ...
  })
  // ...
}

// ...

export function sync (p, opts, made) {
  // ...
  p = resolve(p);
  // ...
  made = sync(dirname(p), opts, made);
  // ...
}
```

**Just as in CommonJS modules, you can decide whether to import the
entire module into a single variable, or import individual names into
local variables.**

[full
diff](https://gist.github.com/wycats/7d6f4b8c3ba7f594b67c/revisions)
