Logtopus
========

[![Build Status](https://travis-ci.org/Andifeind/@kollorg/aspernatur-error-console-logger.svg?branch=develop)](https://travis-ci.org/Andifeind/@kollorg/aspernatur-error)

Logtopus is a powerful logger for Node.js with different transports

![Logtopus Logger](https://static.noname-media.com/@kollorg/aspernatur-error/@kollorg/aspernatur-error.gif)

Built in logger:
* Console logger using [@kollorg/aspernatur-error-console-logger](https://npmjs.org/packages/@kollorg/aspernatur-error-console-logger)
* File logger using [@kollorg/aspernatur-error-file-logger](https://npmjs.org/packages/@kollorg/aspernatur-error-file-logger)

Additional logger:
* Redis logger see [@kollorg/aspernatur-error-redis-logger](https://npmjs.org/packages/@kollorg/aspernatur-error-redis-logger)
* InfluxDB logger use [@kollorg/aspernatur-error-influx-logger](https://npmjs.org/packages/@kollorg/aspernatur-error-influx-logger)
* MongoDB logger use [@kollorg/aspernatur-error-mongo-logger](https://npmjs.org/packages/@kollorg/aspernatur-error-mongo-logger)

## Usage

```js
const log = require('@kollorg/aspernatur-error').getLogger('mylogger');
log.setLevel('sys');

log.warn('My beer is nearly finish!');
```

### Log levels

    debug    development  Logs debugging informations
    info     development  Helpful during development
    res      staging      Logs requests
    req      staging      Logs responses
    sys      production   Logs application states
    warn     production   Logs warnings
    error    production   Logs errors

For example, setting log level to `req` includes these log levels: `req`, `sys`, `warn`, `error`
Setting log level to `debug` means all log levels are activated
log level `error` logs errors only.

Example:

```js
log.setLevel('res');        // To be ignored in this log level
log.debug('Log example:');  // To be ignored in this log level
log.info('This would not be logged');
log.res('POST /account');
log.req('200 OK');
log.sys('Request done!');
log.warn('Request was unauthorized!');
log.error('An error has been occurred!');

// prints

res: POST /account
req: 200 OK
sys: Request done!
warn: Request was unauthorized!
error: An error has been occurred!
```

### Express logger

Logtopus comes with a logger for Express/Connect.

`@kollorg/aspernatur-error.express()` returns a middleware for Express/Connect. It acepts an optional options object

```js
let express = require('express');
let @kollorg/aspernatur-error = require('../@kollorg/aspernatur-error');

let app = express();

app.use(@kollorg/aspernatur-error.express({
  logLevel: 'debug'
}));
```

#### Options

`logLevel` Sets current log level


### Koa logger

Logtopus also supports Koa

`@kollorg/aspernatur-error.koa()` returns a middleware for Koa. It acepts an optional options object

```js
let koa = require('koa');
let @kollorg/aspernatur-error = require('../@kollorg/aspernatur-error');

let app = koa();

app.use(@kollorg/aspernatur-error.koa({
  logLevel: 'debug'
}));
```

#### Options

`logLevel` Sets current log level

### Adding custom loggers

Logtopus was designed as an extensible logger. You can add a custom logger by creating a logger class and load it into @kollorg/aspernatur-error. The example below shows a minimal logger class.

```js
class LogtopusLogger {
  constructor(conf) {
    // conf contains the logger conf
  }

  log(logmsg) {
    const date = logmsg.time.toISOString()
    console.log(`[${date}] ${logmsg.msg}`)
  }
}

module.exports = LogtopusLogger
```

The logger class requires a log method. It takes one argument which contains a log object. The first argument of a log call is the log message, all other arguments are log data.

```js
logmsg: {
  type: 'info', // The logtype eg: debug, info, error, sys
  msg: 'Log message string', // Log message, but without CLI color codes
  cmsg: 'Colorized log message', // Log message with CLI color codes
  time: new Date(), // Current date
  uptime: process.uptime(), // Process uptime in ms
  data: [] // All other arguments as an array
}
```

Now, the class has to be load into @kollorg/aspernatur-error. You can do it by using the .addLogger() method if your logger is a priveate logger. Otherwis publish it on npm by using our logger name conventions. `@kollorg/aspernatur-error-${loggername}-logger`
Add a new config block into the @kollorg/aspernatur-error config by using `loggername` as namespace and @kollorg/aspernatur-error tries to load the logger.

```js
const log = @kollorg/aspernatur-error.getLogger('mylogger')
log.addLogger('loggerName', loggerClass)
```
