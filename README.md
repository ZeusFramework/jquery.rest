jQuery REST Client
=====
v0.0.1a

*Note: These docs are a work in progress.*

Summary
---
A jQuery plugin for easy consumption of REST APIs

Downloads
---

* [Development Version](http://raw.github.com/jpillora/jquery.rest/gh-pages/dist/jquery.rest.js)
* [Production Version](http://raw.github.com/jpillora/jquery.rest/gh-pages/dist/jquery.rest.min.js)

Basic Usage
---

The following HTML:

``` html
<!-- jQuery -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js"></script>

<!-- jQuery rest -->
<script src="//raw.github.com/jpillora/jquery.rest/gh-pages/dist/jquery.rest.min.js"></script>

<script>
  var client = new $.RestClient('/api/rest/');

  client.add('foo');
  client.foo.add('baz');
  client.add('bar');

  client.show();
  // CONSOLE SAYS:
  // foo: /api/rest/foo/:ID_0/
  // baz: /api/rest/foo/:ID_0/baz/:ID_1/
  // bar: /api/rest/bar/:ID_0/

  //BASIC EXAMPLES
  client.foo.create({a:21,b:42});
  // POST /api/rest/foo/ (with data a=21 and b=42)
  client.foo.read();
  // GET /api/rest/foo/
  client.foo.read("42");
  // GET /api/rest/foo/42/
  client.foo.update("42");
  // PUT /api/rest/foo/42/
  client.foo.delete("42");
  // DELETE /api/rest/foo/42/

  //NESTED RESOURCES
  client.foo.baz.read("42");
  // GET /api/rest/foo/42/baz/
  client.foo.baz.read("42", "21");
  // GET /api/rest/foo/42/baz/21/

  //RESULTS USE '$.Deferred'
  client.foo.read().success(function(data) {
    alert('Hooray ! I have: ' + data.foo);
  });

</script>
```

Conceptual Overview
---
Create a client.
Construct your API.
Make requests.

Todo
---
* CSRF Stuff
* Method Override Header

Contributing
---
Issues and Pull-requests welcome, though please add tests. To build and test: `cd *dir*` then `npm install` then `grunt`.

Change Log
---

v0.0.1

* Alpha Version