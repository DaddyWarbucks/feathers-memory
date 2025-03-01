# feathers-memory

[![Greenkeeper badge](https://badges.greenkeeper.io/feathersjs-ecosystem/feathers-memory.svg)](https://greenkeeper.io/)

[![Build Status](https://travis-ci.org/feathersjs-ecosystem/feathers-memory.png?branch=master)](https://travis-ci.org/feathersjs-ecosystem/feathers-memory)
[![Dependency Status](https://img.shields.io/david/feathersjs-ecosystem/feathers-memory.svg?style=flat-square)](https://david-dm.org/feathersjs-ecosystem/feathers-memory)
[![Download Status](https://img.shields.io/npm/dm/feathers-memory.svg?style=flat-square)](https://www.npmjs.com/package/feathers-memory)

A [Feathers](https://feathersjs.com) service adapter for in-memory data storage that works on all platforms.

```bash
$ npm install --save feathers-memory
```

> __Important:__ `feathers-memory` implements the [Feathers Common database adapter API](https://docs.feathersjs.com/api/databases/common.html) and [querying syntax](https://docs.feathersjs.com/api/databases/querying.html).


## API

### `service([options])`

Returns a new service instance initialized with the given options.

```js
const service = require('feathers-memory');

app.use('/messages', service());
app.use('/messages', service({ id, startId, store, events, paginate }));
```

__Options:__

- `id` (*optional*, default: `'id'`) - The name of the id field property.
- `startId` (*optional*, default: `0`) - An id number to start with that will be incremented for every new record (unless it is already set).
- `store` (*optional*) - An object with id to item assignments to pre-initialize the data store
- `events` (*optional*) - A list of [custom service events](https://docs.feathersjs.com/api/events.html#custom-events) sent by this service
- `paginate` (*optional*) - A [pagination object](https://docs.feathersjs.com/api/databases/common.html#pagination) containing a `default` and `max` page size
- `whitelist` (*optional*) - A list of additional query parameters to allow
- `multi` (*optional*) - Allow `create` with arrays and `update` and `remove` with `id` `null` to change multiple items. Can be `true` for all methods or an array of allowed methods (e.g. `[ 'remove', 'create' ]`)

Along with the common Feathers query syntax, this service also supports using the `$unset` data property similar to Mongo/Mongoose to delete properties. But, unlike Mongo/Mongoose it does not support dot notation.

```js
await service.patch(1, { $unset: { name: "" } });
```

## Example

Here is an example of a Feathers server with a `messages` in-memory service that supports pagination:

```
$ npm install @feathersjs/feathers @feathersjs/express @feathersjs/socketio @feathersjs/errors feathers-memory
```

In `app.js`:

```js
const feathers = require('@feathersjs/feathers');
const express = require('@feathersjs/express');
const socketio = require('@feathersjs/socketio');

const memory = require('feathers-memory');

// Create an Express compatible Feathers application instance.
const app = express(feathers());
// Turn on JSON parser for REST services
app.use(express.json());
// Turn on URL-encoded parser for REST services
app.use(express.urlencoded({ extended: true }));
// Enable REST services
app.configure(express.rest());
// Enable REST services
app.configure(socketio());
// Create an in-memory Feathers service with a default page size of 2 items
// and a maximum size of 4
app.use('/messages', memory({
  paginate: {
    default: 2,
    max: 4
  }
}));
// Set up default error handler
app.use(express.errorHandler());

// Create a dummy Message
app.service('messages').create({
  text: 'Message created on server'
}).then(message => console.log('Created message', message));

// Start the server.
const port = 3030;

app.listen(port, () => {
  console.log(`Feathers server listening on port ${port}`)
});
```

Run the example with `node app` and go to [localhost:3030/messages](http://localhost:3030/messages).

## License

Copyright (c) 2017

Licensed under the [MIT license](LICENSE).
