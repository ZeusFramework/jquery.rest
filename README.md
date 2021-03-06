jQuery REST Client
=====
v0.0.4

[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/fb83bb834123c2ede226e1931ea6956a "githalytics.com")](http://githalytics.com/jpillora/jquery.rest)

[![jaime tracking](https://jpillora-secure.appspot.com/track)](http://www.jpillora.com)

Summary
---
A jQuery plugin for easy consumption of RESTful APIs

Downloads
---

* [Development Version](http://jpillora.github.com/jquery.rest/dist/jquery.rest.js)
* [Production Version](http://jpillora.github.com/jquery.rest/dist/jquery.rest.min.js)

Features
---
* Simple
* Uses jQuery Deferred for Asynchonous chaining
* Basic Auth Support
* Helpful Error Messages

Basic Usage
---

1. Create a client.
2. Construct your API.
3. Make requests.

First setup your page:

``` html
<!-- jQuery -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js"></script>

<!-- jQuery rest -->
<script src="http://jpillora.github.com/jquery.rest/dist/jquery.rest.js"></script>
<!-- WARNING: I advise not using this link, instead download and host this library on your own server as GitHub has download limits -->

<script>
  // Examples go here...
</script>
```

Hello jquery.rest
``` javascript
var client = new $.RestClient('/rest/api/');

client.add('foo');

client.foo.read();
// GET /rest/api/foo/
client.foo.read(42);
// GET /rest/api/foo/42/
client.foo.read('forty-two');
// GET /rest/api/foo/forty-two/
```

Retrieving Results (Uses [jQuery's $.Deferred](http://api.jquery.com/category/deferred-object/))
``` javascript
var client = new $.RestClient('/rest/api/');

client.add('foo');

var request = client.foo.read();
// GET /rest/api/foo/
request.done(function (data){ 
  alert('I have data: ' + data);
});

// OR simply:
client.foo.read().done(function (data){ 
  alert('I have data: ' + data);
});
```


More Examples
---

##### Nested Resources
``` javascript

var client = new $.RestClient('/rest/api/');

client.add('foo');
client.foo.add('baz');

client.foo.read();
// GET /rest/api/foo/
client.foo.read(42);
// GET /rest/api/foo/42/

client.foo.baz.read();
// GET /rest/api/foo/???/baz/???/
// !ERROR! Invalid number of ID arguments, required 1 or 2, provided 0
client.foo.baz.read(42);
// GET /rest/api/foo/42/baz/
client.foo.baz.read(42,21);
// GET /rest/api/foo/42/baz/21/

```


##### Basic CRUD Verbs
``` javascript

var client = new $.RestClient('/rest/api/');

client.add('foo');

// C
client.foo.create({a:21,b:42});
// POST /rest/api/foo/ (with data a=21 and b=42)
// Note: data can also be stringified to: {"a":21,"b":42} in this case, see options below

// R
client.foo.read();
// GET /rest/api/foo/
client.foo.read(42);
// GET /rest/api/foo/42/

// U
client.foo.update(42, {my:"updates"});
// PUT /rest/api/42/   my=updates

// D
client.foo.delete(42);
client.foo.del(42); // if JSLint is complaining...
// DELETE /rest/api/foo/42/
```

##### Adding Custom Verbs
``` javascript

var client = new $.RestClient('/rest/api/');

client.add('foo');
client.foo.addVerb('bang', 'PATCH');

client.foo.bang({my:"data"});
//PATCH /foo/bang/   my=data
client.foo.bang(42,{my:"data"});
//PATCH /foo/42/bang/   my=data
```

##### Basic Authentication
``` javascript
var client = new $.RestClient('/rest/api/', {
  username: 'admin',
  password: 'secr3t'
});

client.add('foo');

client.foo.read();
// GET /rest/api/foo/
// With header "Authorization: Basic YWRtaW46c2VjcjN0"
```

##### Caching
``` javascript
var client = new $.RestClient('/rest/api/', {
  cache: 5 //This will cache requests for 5 seconds
  cachableTypes: ["GET"] //This defines what method types can be cached (this is already set by default)
});

client.add('foo');

client.foo.read().done(function(data) {
  //'client.foo.read' is now cached for 5 seconds
});

// wait 3 seconds...

client.foo.read().done(function(data) {
  //data returns instantly from cache
});

// wait another 3 seconds (total 6 seconds)...

client.foo.read().done(function(data) {
  //'client.foo.read' cached result has expired
  //data is once again retrieved from the server
});

// Note: the cache can be cleared with:
client.cache.clear();

```

##### Override Options
``` javascript

var client = new $.RestClient('/rest/api/');

client.add('foo', {
  stringifyData: true,
  cache: 5
});

client.foo.add('bar', {
  cache: 10,
});

client.foo.create({a:1});
// POST /rest/api/foo/ (stringifies data and uses a cache timeout of 5)

client.bar.create(42,{a:2});
// POST /rest/api/foo/42/bar/ (still stringifies data though now uses a cache timeout of 10)

```

##### Fancy URLs
``` javascript
var client = new $.RestClient('/rest/api/');
```
Say we want to create an endpoint `/rest/api/foo-fancy-1337-url/`, instead of doing:
``` javascript
client.add('foo-fancy-1337-url');

client['foo-fancy-1337-url'].read(42);
// GET /rest/api/foo-fancy-1337-url/42
```
Which is bad and ugly, we do:
``` javascript
client.add('foo', { url: 'foo-fancy-1337-url' });

client.foo.read(42);
// GET /rest/api/foo-fancy-1337-url/42
```

##### Query Parameters
``` javascript
var client = new $.RestClient('/rest/api/');

client.add('foo');

client.read({bar:42});
// GET /rest/api/foo?bar=42
```
*Note: Convience option for query parameters when POSTing is in progress* 

##### Show API Example
``` javascript
var client = new $.RestClient('/rest/api/');

client.add('foo');
client.add('bar');
client.foo.add('baz');

client.show();

```
Console should say:
```
ROOT: /rest/api/
  foo: /rest/api/foo/:ID_1/
    create: POST
    read: GET
    update: PUT
    delete: DELETE
    baz: /rest/api/foo/:ID_1/baz/:ID_2/
      create: POST
      read: GET
      update: PUT
      delete: DELETE
  bar: /rest/api/bar/:ID_1/
    create: POST
    read: GET
    update: PUT
    delete: DELETE 
```

##### Simplify `client` 
``` javascript

var client = new $.RestClient('/rest/api/');

client.add('forum');
client.forum.add('post');
client.forum.post.add('comment');
```

Instead of:
``` javascript
client.forum.post.comment.read(42,21,7);
client.forum.post.comment.update(42,21,7);
```

You can do:
``` javascript
var comment = client.forum.post.comment;
comment.read(42,21,7);
comment.update(42,21,7);
```

##### Global/Singleton Example
``` javascript

$.client = new $.RestClient('/rest/api/');

// in another file...

$.client.add('foo');
```
*Note: This is not best practise, use RequireJS or similar instead !*

API
---

#### new $.RestClient( [ `url` ], [ `options` ] )

Instantiates and returns the root resource. Below denoted as `client`.

#### `client`.add( `name`, [ `options` ] )

Instaniates a nested resource on `client`. Internally this does another `new $.RestClient` though instead of setting it as root, it will add it as a nested (or child) resource as a property on the current `client`.

Newly created nested resources iterate through their `options.verbs` and addVerb on each.

Note: The url of each of these verbs is set to `""`.

See default `options.verbs` [here](https://github.com/jpillora/jquery.rest/blob/gh-pages/src/jquery.rest.coffee#L39).

#### `client`.addVerb( `name`, `method`, [ `options` ] )

Instaniates a new Verb function property on the `client`.

Note: `name` is used as the `url` if `options.url` is not set.

#### `client`.`verb`( [`id1`], ..., [`idN`], [`data`])

All verbs use this signature. Internally, they are all *essentially* calls to `$.ajax` with custom options depending on the parent `client` and `options`.

`id`s must be a string or number.

`data` is a [jQuery Ajax](http://api.jquery.com/jQuery.ajax/) Options Object's data property. If `ajax.data` is set on the `client` this `data` will extend it.

Note: A helpful error will be thrown if invalid arguments are used.

Options
---

The `options` object is a plain JavaScript option that may only contain the properties listed below.

See defaults [here](https://github.com/jpillora/jquery.rest/blob/gh-pages/src/jquery.rest.coffee#L32)

*Important*: Both resources and verbs inherit their parent's options !

#### cache

A number reprenting the number of seconds to used previously cached requests. When set to `0`, no requests are stored.

### cachableTypes

An array of strings reprenting the HTTP method types that can be cached. Is `["GET"]` by default. 

#### verbs

A plain object used as a `name` to `method` mapping.

The default `verbs` object is set to:
``` javascript
{
  'create': 'POST',
  'read'  : 'GET',
  'update': 'PUT',
  'delete': 'DELETE'
}
```

For example, to change the default behaviour of update from using PUT to instead use POST, set the `verbs` property to `{ update: 'POST' }`

### url

A string representing the URL for the given resource or verb.

Note: url is not inherited, if it is not set explicitly, the name is used as the URL.

### stringifyData

When set, will pass all POST data through `JSON.stringify` (polyfill required for IE<=8).

### stripTrailingSlash

When set, the trailing slash will be stripped off the URL.

### username and password

When both username and password are set, all ajax requests will add an 'Authorization' header. Encoded using `btoa` (polyfill required not non-webkit).

### ajax

A [jQuery Ajax](http://api.jquery.com/jQuery.ajax/) Options Object

*Note: Want more options ? Open up a New Feature Issue above.*

Conceptual Overview
---

This plugin is made up nested 'Resource' classes. Resources contain options, child Resources and child Verbs. Verbs are functions that execute various HTTP requests.
Both `new $.RestClient` and `client.add` construct new instances of Resource, however the former will create a root Resource with no Verbs attached, whereas the latter will create child Resources with all of it's `options.verbs` attached.

Since each Resource can have it's own set of options, at instantiation time, options are inherited from parent Resources, allowing one default set of options with custom options on child Resources.

Todo
---
* CSRF
* Method Override Header
* Add Tests

Contributing
---
Issues and pull-requests welcome. To build: `cd *dir*` then `npm install` then `grunt`. Grunt wil compile the source `jquery.rest.coffee` into `.js` which will then get minified `min.js`.

Change Log
---

v0.0.4

* Simplified API

v0.0.3

* Added into the jQuery Plugin Repo

v0.0.2

* Manually Tested

v0.0.1

* Alpha Version

