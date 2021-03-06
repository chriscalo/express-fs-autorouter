# express-fs-autorouter
File-based auto routing for Express

Given a project directory of `.js` files like the following:

``` text
/project
└── api
    ├── index.js
    ├── login.js
    └── accounts
        └── index.js
        └── [id]
            └── index.js
```

Passing the `api` directory to `express-fs-autorouter` will create an Express
router to automatically map URL paths to corresponding files:

``` text
/               =>  api/index.js
/login          =>  api/login.js
/accounts/      =>  api/accounts/index.js
/accounts/:id/  =>  api/accounts/[id]/index.js
```

## Installation

``` sh
yarn add express-fs-autorouter
# OR
npm install express-fs-autorouter
```

## Usage

In your Express app, use `autoRouter()`, passing in an absolute path to the
directory to search for `.js` handler files. (If you pass no path, it will use
`process.cwd()`.)

``` js
const { resolve } = require("path");
const express = require("express");
const autoRouter = require("express-fs-autorouter");

const app = express();

// auto-routing
const apiDir = resolve(__dirname, "api");
app.use(autoRouter(apiDir));

// start server
app.listen(8080);
```

In each handler file, export a connect-/Express-style handler function:

``` js
module.exports = (req, res) => {
  res.send("Hello, World!");
};
```

You can also export an `express()` subapp instance, which has the same
`(req, res, next)` signature, but due to [how Express works][express-bug], this
will not work with parameterized folders like in `/accounts/[id]/index.js`:

[express-bug]: https://github.com/expressjs/express/issues/696

``` js
const express = require("express");

const handler = express();
module.exports = handler;

handler
  .route("*")
  // this example only handles GET requests
  .get((req, res) => {
    res.send("Hello, World!");
  });
```
