pouchdb.mapreduce.noeval
=====

Fork of the PouchDB [map/reduce project](https://github.com/pouchdb/mapreduce) which avoids using `eval()`. Thus, it allows using PouchDB in environments with strict policy against dynamic script evaluation, such as Chrome Packaged Apps or Adobe AIR runtime.

Note that this kind of treatment is not technically required for Chrome Extensions or (now deprecated) Chrome Apps using manifest in version 1. For these kind of applications, the content security policy can be relaxed.

However, using `eval()` in new Chrome Apps would also require putting the page using the script in a sandbox, which in turn renders PouchDB unusable, as sandboxed page doesn't have access to IndexedDB. Using this plugin is effectively the only way to use PouchDB in a packaged app without putting the original MapReduce plugin in a sandbox and setting a wrapper around it to communicate with the rest of library via window.postMessage() calls.

This plugin works by running a simple regex over your `map` functions, so it only works for basic usages. The most complex kind of map function you can do is e.g.

```js
function(doc) {
  if (doc.type === 'person') {
    emit(doc.name, 1);
  }
}
```

`reduce` functions are not supported.  Your mileage may vary; this is mostly a quick-and-dirty solution so you can start using things like design docs for secondary indexes (which require string evaluation; there's no way around it).

This plugin will also fall back to the regex only if  `eval()` throws an error, so you
can still use it as-is in environments that support `eval()`.

Usage
-----

To use this plugin, include it after `pouchdb.js` in your HTML page:

```html
<script src="pouchdb.js"></script>
<script src="pouchdb.mapreduce-noeval.js"></script>
```

This plugin is also available from Bower:

```
bower install pouchdb-mapreduce-noeval
```

Or to use it in Node.js, just npm install it:

```
npm install pouchdb-mapreduce-noeval
```

And then attach it to the `PouchDB` object:

```js
var PouchDB = require('pouchdb');
PouchDB.plugin(require('pouchdb-mapreduce-noeval'));
```

Building
----

    npm run build

Testing
----

### In Node

Run tests with `npm test` and coverage of tests with `npm test --coverage` install dependencies with `npm install`

If you have mocha installed globally you can run single test with:
```
TEST_DB=local mocha --reporter spec --grep search_phrase
```
In TEST_DB environment variable specify database that PouchDB should use (see package.json)

### In the browser

Run `npm run dev` and then point your favorite browser to [http://127.0.0.1:8001/test/index.html](http://127.0.0.1:8001/test/index.html).
