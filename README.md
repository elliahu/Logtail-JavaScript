# Logtail JavaScript

Collect logs from both the Node.js backend and your frontend.

# Installation

You can install Logtail to use it in both backend (Node.js 12 or newer) and frontend code.

## Backend only

Install Logtail library for your Node.js backend code using the `npm` package manager. We are currently supporting Node version 12 or newer.

```bash
npm install @logtail/node
```

Then you can either require it or import it into your codebase 

```jsx
// require logtail class
const { Logtail } = require("@logtail/node");

// or import if you're using ES modules:
import { Logtail } from "@logtail/node";
```

## Frontend only

Install Logtail library for your frontend code using the npm package manager:

```bash
npm install @logtail/browser
```

Then import the Logtail class:

```jsx
// import logtail class
import { Logtail } from "@logtail/browser";
```

Another option is to use Content Delivery Network. You can import Logtail using the CDN by adding the following line into a page header.

```bash
<script src="https://unpkg.com/browse/@logtail/browser@latest/dist/umd/logtail.js"></script>
```

## Both backend and frontend

If your project contains both Node.js backend code and frontend code as well, you can use universal package `@logtail/js` which combines the `node` and `browser` packages:

First, install the Logtail library using the `npm` package manager:

```bash
npm install @logtail/js
```

Then import or require it in your code:

```jsx
// in your backend code:
const { Node: Logtail } = require("@logtail/js");

// in your frontend code:
import { Browser as Logtail } from "@logtail/js";
```

# Example project

To help you get started with logging using Logtail, we have created an example project. 

Make sure you have `npm` installed and also make sure you have [Node.js installed](https://nodejs.org/en/download/) and up-to-date.

## Install the example project

Clone the git repository or download it as a ZIP and extract it into a select directory which will be your projects directory.

Then run the following command to install all dependencies (`@logtail/js`):

```bash
npm install
```

## Run the example project

To run the example project, simply run the following command and don't forget to replace the `<source-token>` with your actual source toke, which you can find in source settings.

```bash
node index.js <source-token>
```

You should see the following output:

```
Output:
All done! You can check your logs now.
```

This will generate a total of 4 logs, which can be displayed in your Logtail live monitor.

An explanation of the example project follows below.

# Logging

The `logger` instance we created in the installation section is used to send log messages to Logtail. It provides 4 logging methods for the 4 default log levels. The log levels and their method are:

- **DEBUG** - Send debug messages using the `debug()` method
- **INFO** - Send informative messages about the application progress using the `info()` method
- **WARN** - Report non-critical issues using the `Warn()` method
- **ERROR** - Send messages about serious problems using the `error()` method

## Logging example

To send a log message of select log level, use the corresponding method. In this example, we will send the **DEBUG** level log and **ERROR** level log.

```jsx
// Send debug level log using the debug() method
logger.debug("I am using Logtail!");

// Send error level log using the error() method
logger.error("Oops! An runtime ERROR occurred!");
```

This will create the following JSON output:

```json
{
   "dt":"2022-02-01 12:01:10.127 UTC",
   "context":{
      "runtime":{
         "column_integer":"21",
         "file_string":"node_modules/@logtail/core/dist/cjs/base.js",
         "line_integer":"141",
         "type_string":"Node",
         "function_string":"debug",
         "method_string":"debug"
      },
      "system":{
         "main_file_string":"/mnt/d/js_logtail/index.js",
         "pid_integer":"4193"
      }
   },
   "level_string":"debug",
   "message_string":"I am using Logtail!"
}

{
   "dt":"2022-02-01 12:01:10.127 UTC",
   "context":{
      "runtime":{
         "column_integer":"8",
         "file_string":"index.js",
         "line_integer":"40",
         "type_string":"Object"
      },
      "system":{
         "main_file_string":"/mnt/d/js_logtail/index.js",
         "pid_integer":"4193"
      }
   },
   "level_string":"error",
   "message_string":"Oops! An runtime ERROR occurred!"
}

```

## Logging structured data

You can log additional structured data to help you troubleshoot your application much quicker. Pass the structured data as the second argument to the select login method as shown in the example below:

```jsx
logger.warn("Something is not quite right, better check on it.",{
    user:{
        username:"someuser",
        email:"someuser@example.com"
    },
    additional_info:{
        tried_accessing: "/url/of/error"
    }
});
```

This will create the following JSON output:

```json
{
   "dt":"2022-02-01 12:01:10.127 UTC",
   "context":{
      "runtime":{
         "column_integer":"8",
         "file_string":"index.js",
         "line_integer":"29",
         "type_string":"Object"
      },
      "system":{
         "main_file_string":"/mnt/d/js_logtail/index.js",
         "pid_integer":"4193"
      }
   },
   "level_string":"warn",
   "message_string":"Something is not quite right, better check on it.",
   "additional_info":{
      "tried_accessing_string":"/url/of/error"
   },
   "user":{
      "email_string":"someuser@example.com",
      "username_string":"someuser"
   }
}
```

## Middleware

You can intercept every logged item and modify it before it's pushed to Logtail. This could be useful for example for adding the current user's ID to the log:

```jsx
// intercept the log and add userId to the content
async function enrichLogs(log) {
    return {
        ...log,
        userId: getCurrentUserId()
    };
}

// tell logtail to use the intercept function
logtail.use(enrichLogs);
```

## TypeScript support

If you're using Logtail in a TypeScript codebase, you can take advantage of our types. The custom middleware function from the previous example could look like this:

```jsx
import { ILogtailLog } from "@logtail/types";

async function enrichLogs(log: ILogtailLog): Promise<ILogtailLog> {
    return {
        ...log,
        userId: getCurrentUserId()
    };
}
```

# Integrations

We provide integrations with several existing frameworks and logging solutions.

## Koa

Install the following `@logtail/koa` NPM package and then attach the Logtail instance as a middleware to your Koa instance:

```bash
npm install @logtail/koa
```

```jsx
const Koa = require("koa");
const { Logtail } = require("@logtail/koa");

// Create a new Koa instance
const koa = new Koa();

// Create a new Logtail client
const logtail = new Logtail("<source-token>");

// Attach Koa to enable HTTP request logging
logtail.attach(koa);
```

Don't forget to replace the `<source-token>` with your actual source toke, which you can find in source settings.

### How the middleware works

**Successful requests**

A *successful* request is one that returns a non-`4xx` or `5xx` status code and doesn't throw any uncaught errors while handling the requests. These are logged to Logtail using `[LogLevel.Info](https://github.com/logtail/logtail-js/tree/master/packages/types#loglevel)`

**4xx status codes**

These are not considered errors but warnings and log with the same message using `[LogLevel.Warn](https://github.com/logtail/logtail-js/tree/master/packages/types#loglevel)`

**5xx status codes**

Responses that contain a `5xx` status code are considered errors and are logged with `[LogLevel.Error](https://github.com/logtail/logtail-js/tree/master/packages/types#loglevel)`

### Additional logging

Since this Koa plugin extends the regular `[@logtail/node](https://github.com/logtail/logtail-js/tree/master/packages/node)` logger, you can use the `.log|info|warn|error` functions as normal to handle logging anywhere in your app.

## Winston

Install the `@logtail/winston` NPM package and set up the Logtail transport according to the following example

```bash
npm install @logtail/winston
npm install @logtail/node
```

```jsx
const winston = require("winston");
const { Logtail } = require("@logtail/node");
const { LogtailTransport } = require("@logtail/winston");

// Create a Logtail client
const logtail = new Logtail("<source-token>");

// Create a Winston logger - passing in the Logtail transport
const logger = winston.createLogger({
  transports: [new LogtailTransport(logtail)],
});

// Log as normal in Winston - your logs will sync with Logtail.com!
logger.log({
  level: "info", // <-- will use Logtail's `info` log level,
  message: "Some message", // <-- will also be passed to Logtail
});
```

Don't forget to replace the `<source-token>` with your actual source toke, which you can find in source settings.

## Bunyan

Install the `@logtail/bunyan` NPM package and set up the Logtail stream according to the following example:

```bash
npm install @logtail/bunyan
npm install @logtail/node
```

```jsx
const bunyan = requrie("bunyan");
const { Logtail } = require("@logtail/node");
const { LogtailStream } = require("@logtail/bunyan");

// Create a Logtail client
const logtail = new Logtail("logtail-source-token");

// Create a Bunyan logger - passing in the Logtail stream
const logger = bunyan.createLogger({
  name: "Example logger",
  level: "debug",
  streams: [
    {
      stream: new LogtailStream(logtail),
    },
  ],
});

// Log as normal in Bunyan - your logs will be sent to Logtail.com
logger.info("Hello from Bunyan");
```