---
title: JavaScript Modules
---

The next version of JavaScript comes with a module system heavily
inspired by Node.js modules.<br>Here's how it works.

# Creating a Module

We're going to build a simple `asap` module, which lets you schedule some
work to happen as soon as possible, but asynchronously. In Node.js, you
can do this via `process.nextTick`, but there are different approaches
that work in various browsers. We're going to build a module that works
everywhere.[<sup>1</sup>](#footnote-1)

We start by creating a new file for this module. We'll call it `asap.js`.
The module provides a single primary function, which JavaScript calls
the *default export*. You export a default value from a module by using
`export default`.

```js
var asap;
var isNode = typeof process !== "undefined" &&
             {}.toString.call(process) === "[object process]";

if (isNode) {
  asap = process.nextTick;
} else if (typeof setImmediate !== "undefined") {
  asap = setImmediate;
} else {
  asap = setTimeout;
}

export default asap;
```

# Importing a module

To import `asap` in another module, we use the import syntax:

```js
import asap from "asap";

asap(function() {
  console.log("hello async world!");
});
```

This syntax takes the default function exported from `asap` and stores it
in the variable `asap`, which we can then use to call it.

# Named exports

Sometimes modules need multiple exports, which their consumers can refer
to individually by name.

For example, jQuery has a single primary export, (the `jQuery`
function), and several additional named exports (`ajax`, `getJSON`,
`animate`, etc.).  In Node.js, the `mkdirp` module has a single default
export, which recursively creates a directory, and a named export called
`sync`, which does the same thing, but synchronously.

In our case, in addition to the default export, the `asap` module might
wish to provide a `later` function, which schedules a function to run
later, after other network or UI work has a chance to occur.

Our module looks the same as before, except we add a new export
declaration.

```js
var asap;
var isNode = typeof process !== "undefined" &&
             {}.toString.call(process) === "[object process]";

if (isNode) {
  asap = process.nextTick;
} else if (typeof setImmediate !== "undefined") {
  asap = setImmediate;
} else {
  asap = setTimeout;
}

export default asap;
export var later = isNode ? process.setImmediate : asap;
```

# Named imports

Now that we've exported `later`, we can import it in another module.

```js
import { later } from "asap";

later(function() {
  console.log("Running after other network events");
});
```

For the curious, you can import both the default export and a number of
named exports in the same `import`:

```js
import asap, { later } from "asap";
```

And that's all there is to it!

# Conveniences

## Renaming named imports

Sometimes when importing a named export, you want to give it its own
local name.

```js
import { unlink as rm } from "fs";

rm(filename, function(err) { /* check errors */ });
```

## Importing into a namespace

It can be convenient to import all of a module's named exports into a
single local namespace.

```js
import * as fs from "fs";

fs.unlink(filename, function(err) { /* check errors */ });
```

## Shorter named exports

You can make any declaration in JavaScript (like `var` or `function`)
a named export by prefixing it with export.

```js
// exports this function as "requestAnimationFrame"
export function requestAnimationFrame() {
  // cross-browser requestAnimationFrame
}

// exports document.location as "location"
export var location = document.location; 
```

This also works with new declarations, like `class` and `let`.

```js
// exports this class as "File"
export class File() { /* implementation */ }

// exports "0.6.3" as "VERSION"
export let VERSION = "0.6.3";
```

These names are also available in the module's local scope, so you can
use them in other functions.

## Grouping named exports

You can export any number of local variables together.

```js
export { getJSON, postJSON, animate };

function getJSON() {
  // implementation
}

function postJSON() {
  // implementation
}

function animate() {
  // implementation
}
```

You can put the grouped export declaration anywhere in the file, so you
can define your imports and exports next to each other at the top of
your modules if you wish.

# Features

JavaScript modules have a number of nice features that make important
use cases smooth and seamless, including refactoring and tooling.

1. JavaScript modules support late bound cycles between modules for both
   the default export and named exports. It just works.
2. JavaScript modules separate the names that exist on the default
   export (and their prototype chains) and other named exports, avoiding
   collisions.
3. JavaScript modules make it easy to determine exactly what you are
   importing by just looking at the syntax. That improves error
   messages, but also makes it easier to build tools like browserify and
   JSHint that work reliably without caveats.

# Notes

<span id="footnote-1"><sup>1</sup> A production-quality version of this library would be more
detailed, but this will do for our purposes.</span>
