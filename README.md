# zip-stream v0.4.0-alpha [![Build Status](https://travis-ci.org/ctalkington/node-zip-stream.svg?branch=master)](https://travis-ci.org/ctalkington/node-zip-stream)

zip-stream is a streaming zip archive generator. It was built to be a successor to [zipstream](https://npmjs.org/package/zipstream). Dependencies are kept to a minimum through the use of many of node's built-in modules including the use of zlib module for compression.

*This project has become a wrapper around the `ZipArchiveOutputStream` "class" found in the [compress-commons](https://www.npmjs.org/package/compress-commons) project. Effort has been made to retain much of the API set forth prior to v0.4.0.*

[![NPM](https://nodei.co/npm/zip-stream.png)](https://nodei.co/npm/zip-stream/)

### Install

```bash
npm install zip-stream --save
```

You can also use `npm install https://github.com/ctalkington/node-zip-stream/archive/master.tar.gz` to test upcoming versions.

### Usage

This module is meant to be wrapped internally by other modules and therefore lacks any queue management. This means you have to wait until the previous entry has been fully consumed to add another. Nested callbacks should be used to add multiple entries. There are modules like [async](https://npmjs.org/package/async) that ease the so called "callback hell".

If you want a module that handles entry queueing and much more, you should check out [archiver](https://npmjs.org/package/archiver) which uses this module internally.

```js
var packer = require('zip-stream');
var archive = new packer(); // OR new packer(options)

archive.on('error', function(err) {
  throw err;
});

// pipe archive where you want it (ie fs, http, etc)
// listen to the destination's end, close, or finish event

archive.entry('string contents', { name: 'string.txt' }, function(err, entry) {
  if (err) throw err;
  archive.entry(null, { name: 'directory/' }, function(err, entry) {
    if (err) throw err;
    archive.finalize();
  });
});
```

### Instance API

#### entry(input, data, callback(err, data))

Appends an input source (text string, buffer, or stream) to the instance. When the instance has received, processed, and emitted the input, the callback is fired.

#### finalize()

Finalizes the instance. You should listen to the destination stream's `end`/`close`/`finish` event to know when all output has been safely consumed.

### Instance Options

#### comment `string`

Sets the zip comment.

#### forceUTC `boolean`

If true, forces the entry date to UTC. Helps with testing across timezones.

#### store `boolean`

If true, all entry contents will be archived without compression by default.

#### zlib `object`

Passed to node's [zlib](http://nodejs.org/api/zlib.html#zlib_options) module to control compression. Options may vary by node version.

### Entry Data

#### name `string` `required`

Sets the entry name including internal path.

#### type `string`

Sets the entry type. Defaults to `file` or `directory` if name ends with trailing slash.

#### date `string|Date`

Sets the entry date. This can be any valid date string or instance. Defaults to current time in locale.

#### store `boolean`

If true, entry contents will be archived without compression.

#### comment `string`

Sets the entry comment.

#### mode `number`

Sets the entry permissions.

## Things of Interest

- [Releases](https://github.com/ctalkington/node-zip-stream/releases)
- [Contributing](https://github.com/ctalkington/node-zip-stream/blob/master/CONTRIBUTING.md)
- [MIT License](https://github.com/ctalkington/node-zip-stream/blob/master/LICENSE-MIT)

## Credits

Concept inspired by Antoine van Wel's [zipstream](https://npmjs.org/package/zipstream) module, which is no longer being updated.