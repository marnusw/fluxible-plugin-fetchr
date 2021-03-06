# Fetchr Plugin for Fluxible

[![npm version](https://badge.fury.io/js/fluxible-plugin-fetchr.svg)](http://badge.fury.io/js/fluxible-plugin-fetchr)
[![Build Status](https://travis-ci.org/yahoo/fluxible-plugin-fetchr.svg?branch=master)](https://travis-ci.org/yahoo/fluxible-plugin-fetchr)
[![Dependency Status](https://david-dm.org/yahoo/fluxible-plugin-fetchr.svg)](https://david-dm.org/yahoo/fluxible-plugin-fetchr)
[![devDependency Status](https://david-dm.org/yahoo/fluxible-plugin-fetchr/dev-status.svg)](https://david-dm.org/yahoo/fluxible-plugin-fetchr#info=devDependencies)
[![Coverage Status](https://coveralls.io/repos/yahoo/fluxible-plugin-fetchr/badge.png?branch=master)](https://coveralls.io/r/yahoo/fluxible-plugin-fetchr?branch=master)

Provides isomorphic RESTful service access to your [Fluxible](https://github.com/yahoo/fluxible) application using [fetchr](https://github.com/yahoo/fetchr).

## Usage

```js
var Fluxible = require('fluxible');
var fetchrPlugin = require('fluxible-plugin-fetchr');
var app = new Fluxible();

app.plug(fetchrPlugin({
    xhrPath: '/api' // Path for XHR to be served from
}));
```

Now, when calling the `createContext` method on the server, make sure to send in the request object and optionally pass an `xhrContext` which will be used as parameters for all XHR calls:

```
app.createContext({
    req: req,
    xhrContext: { // Used as query params for all XHR calls
        lang: 'en-US', // make sure XHR calls receive the same lang as the initial request
        _csrf: 'a3fc2d' // CSRF token to validate on the server using your favorite library
    }
});
```

### Registering Your Services

Since the fetchr plugin is in control the `fetchr` class, we expose this through the `registerService` method.

```js
pluginInstance.registerService(yourService);
```

Or if you need to do this from your application without direct access to the plugin

```js
app.getPlugin('FetchrPlugin').registerService(yourService);
```

### Exposing Your Services

Fetchr also contains an express/connect middleware that can be used as your access point from the client.

```js
var server = express();
server.use(pluginInstance.getXhrPath(), pluginInstance.getMiddleware());
```

### Dynamic XHR Paths

The `fetchrPlugin` method can also be passed a `getXhrPath` function that returns the string for the `xhrPath`. This allows you to dynamically set the `xhrPath` based on the current context. For instance, if you're hosting multiple sites and want to serve XHR via a pattern route like `/:site/api`, you can do the following:

```js
app.plug(fetchrPlugin({
    getXhrPath: function (contextOptions) {
        // `contextOptions` is the object passed to `createContext` above
        return contextOptions.req.params.site + '/api';
    }
}));
```

[//]: # (API_START)
## Fetchr Plugin API

### Constructor(options)

Creates a new `fetchr` plugin instance with the following parameters:

 * `options`: An object containing the plugin settings
 * `options.xhrPath` (optional): Stores your xhr path prefix used by client side requests. DEFAULT: '/api'

### Instance Methods

#### getXhrPath

getter for the `xhrPath` option passed into the constructor.

```
var pluginInstance = fetchrPlugin({
    xhrPath: '/api'
});

pluginInstance.getXhrPath(); // returns '/api'
```

#### registerService(service)

[register a service](#Registering Your Services) with fetchr

#### getMiddleware

getter for fetchr's express/connect middleware. See [usage](#Exposing Your Services)

### actionContext Methods

 * `actionContext.service.read(resource, params, [config,] callback)`: Call the read method of a service. See [fetchr docs](https://github.com/yahoo/fetchr) for more information.
 * `actionContext.service.create(resource, params, body, [config,] callback)`: Call the create method of a service. See [fetchr docs](https://github.com/yahoo/fetchr) for more information.
 * `actionContext.service.update(resource, params, body, [config,] callback)`: Call the update method of a service. See [fetchr docs](https://github.com/yahoo/fetchr) for more information.
 * `actionContext.service.delete(resource, params, [config,] callback)`: Call the delete method of a service. See [fetchr docs](https://github.com/yahoo/fetchr) for more information.
 * `actionContext.getServiceMeta()`: The plugin will collect metadata for service responses and provide access to it via this method. This will return an array of metadata objects.
[//]: # (API_STOP)

## License

This software is free to use under the Yahoo! Inc. BSD license.
See the [LICENSE file][] for license text and copyright information.

[LICENSE file]: https://github.com/yahoo/fluxible-plugin-fetchr/blob/master/LICENSE.md
