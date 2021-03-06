# Getting Started
## Installing hapi

Create a new directory `myproject`, and from there:

* Run: `npm init` and follow the prompts, this will generate a package.json file for you.
* Run: `npm install --save hapi` this installs hapi, and saves it in your package.json as a dependency of your project.

That's it! You now have everything you need in order to create a server using hapi.

## Creating a server

The most basic server looks like the following:

```javascript
var Hapi = require('hapi');
var server = new Hapi.Server(3000);

server.start(function () {
    console.log('Server running at:', server.info.uri);
});
```

First, we require hapi. Then we create a new hapi server object, passing in a port
number for the server to listen on. After that, start the server and log that it's
running.

When creating the server object, we can also provide a hostname, IP address, or even
a Unix socket file, or Windows named pipe to bind the server to. For more details, see [the API reference](/api/#hapiserver).

## Adding routes

Now that we have a server we should add one or two routes so that it actually does something. Let's see what that looks like:

```javascript
var Hapi = require('hapi');
var server = new Hapi.Server(3000);

server.route({
    method: 'GET',
    path: '/',
    handler: function (request, reply) {
        reply('Hello, world!');
    }
});

server.route({
    method: 'GET',
    path: '/{name}',
    handler: function (request, reply) {
        reply('Hello, ' + encodeURIComponent(request.params.name) + '!');
    }
});

server.start(function () {
    console.log('Server running at:', server.info.uri);
});
```

Save the above as `server.js` and start the server with the command `node server.js`. Now you'll find that if you visit http://localhost:3000 in your browser, you'll see the text `Hello, world!`, and if you visit http://localhost:3000/stimpy you'll see `Hello, stimpy!`.

Note that we URI encode the name parameter, this is to prevent content injection attacks. Remember, it's never a good idea to render user provided data without output encoding it first!

The `method` parameter can be any valid HTTP method or an asterisk to allow any method. The `path` parameter defines the path including parameters. It can contain optional parameters, numbered parameters, and even wildcards. For more details, see [the routing tutorial](/tutorials/routing).

## Using plugins

A common desire when creating any web application, is an access log. To add some basic logging to our application, let's load the [good](https://github.com/spumko/good) plugin on to our server.

The plugin first needs to be installed:

```bash
npm install --save good
```

Then update your `server.js`:

```javascript
var Hapi = require('hapi');
var Good = require('good');

var server = new Hapi.Server(3000);

server.route({
    method: 'GET',
    path: '/',
    handler: function (request, reply) {
        reply('Hello, world!');
    }
});

server.route({
    method: 'GET',
    path: '/{name}',
    handler: function (request, reply) {
        reply('Hello, ' + encodeURIComponent(request.params.name) + '!');
    }
});

server.pack.register(Good, function (err) {
    if (err) {
        throw err; // something bad happened loading the plugin
    }

    server.start(function () {
        server.log('info', 'Server running at: ' + server.info.uri);
    });
});
```

Now when the server is started you'll see:

```
140625/143008.751, info, Server running at: http://localhost:3000
```

And if we visit `http://localhost:3000/` in the browser, you'll see:

```
140625/143205.774, request, http://localhost:3000: get / {} 200 (10ms)
```

Great! This is just one short example of what plugins are capable of, for more information check out the [plugins tutorial](/tutorials/plugins).

## Packs

You may be wondering: what is the `server.pack` property that the register method is attached to?

It's a pack! Packs are a way for hapi to combine multiple servers into a single unit, and are designed to give a unified interface when working with plugins.

When you create a server, a pack object is automatically created and stored as the `server.pack` property. You can also create a pack directly, and add servers to it later.

For more information about packs, check out the [packs tutorial](/tutorials/packs).

## Everything else

hapi has many, many other capabilities and only a select few are documented in tutorials here. Please use the list to your right to check them out. Everything else is documented in the [API reference](/api) and, as always, feel free to ask question or just visit us on freenode in [#hapi](http://webchat.freenode.net/?channels=hapi).
