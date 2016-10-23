# boilerpl8

> A somewhat opinionated, hot-reloading boilerplate for React + Redux, Express, and SQL.

```
 __        __   _                            _
 \ \      / /__| | ___ ___  _ __ ___   ___  | |_ ___
  \ \ /\ / / _ \ |/ __/ _ \| '_ ` _ \ / _ \ | __/ _ \
   \ V  V /  __/ | (_| (_) | | | | | |  __/ | || (_) |
    \_/\_/ \___|_|\___\___/|_| |_| |_|\___|  \__\___/

   ____        _ _                 _  ___
  | __ )  ___ (_) | ___ _ __ _ __ | |( _ )
  |  _ \ / _ \| | |/ _ \ '__| '_ \| |/ _ \
  | |_) | (_) | | |  __/ |  | |_) | | (_) |
  |____/ \___/|_|_|\___|_|  | .__/|_|\___/
                            |_|
```

## Getting Started

First, create a file in the root of the directory called `.env`.  In here, you should have the 
following structure, at a minimum.  Put any other environment variables you like in there, too.

```sh
DB_PRODUCTION=
DB_TESTING=postgres://test:test@127.0.0.1/project_test
DB_DEVELOPMENT=postgres://user:passsword@127.0.0.1/project
```

Of course, change those DB urls to whatever they need to be on your machine.

Optional environment variables are:
```sh
APPLICATION_NAME=<your application name> <-- defaults to package.json -> name
PORT=<port> <-- defaults to 3000
API_VERSION=<number> <-- defaults to 1
```

Next, run [`yarn`](https://yarnpkg.com/en/docs/install) to install dependencies.

Do yourself a favour and get [redux-dev-tools](https://github.com/gaearon/redux-devtools) for 
Chrome, by the amazing [Dan Abramov](https://github.com/gaearon).  This project is set up to 
support it in development mode.  Have a look at it when you're playing around with the app.  See what 
 happens when you drag the slider back and forth.
 
You also will probably want [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
by [Facebook](https://code.facebook.com/).  It shows you the Chrome Elements layout, with your 
React elements.  Also shows you the props, state and store for every component, and gives you 
access to them via the Chrome console.

## Usage

### Tools & Scripts

This project has a `yarn.lock` for deterministic package installation, but it's completely 
optional.

There are many scripts included.

- `npm run serve:dev` - serves the express server, and starts webpack.
- `npm run serve:dash` - same as `serve:dev`, but uses [`webpack-dashboard`](https://github.com/FormidableLabs/webpack-dashboard) 
for a nice user interface.
- `npm run lint` - runs `eslint` on the entire codebase, using [airbnb's config](https://github.com/airbnb/javascript).
- `npm test` - runs the tests.  All files under `./test`, ending in `.test.js` are run.
- `npm report` - compiles the test coverage, generated by [`nyc`](https://github.com/istanbuljs/nyc) 
during testing, into HTML for easy viewing

The build commands are still a work-in-progress, but have recently gotten a little better.  They 
are:

- `build` - runs clean and build on the client and server
- `clean` - removes all compiled files
- `build:client` - compiles the client-side code and stores it in `./static/bundle.js`
- `build:server` - compiles the code inside of `./src` into `./out`

And finally, 
- `npm start` - runs `forever` on the compiled `./src/index.js` in production mode.
  
### Server

The server follows a fairly straightforward layout.  I don't tend to follow the Rails paradigm of
 storing model controls in the models, but rather inside the RESTful API that will control it.
 
The way that the models are structured lend themselves to this.  All your Sequelize models will 
be attached to the DB, which can be used by `import db from './models'` from wherever you need it
. See the `./models/User.js` file for an example, and it's use inside of `./routes/user.js`.

All routes are required by default into the core router, under `/api/v1/<file_name>` by default.  
Change the version by setting the `API_VERSION` environment variable.  I would recommend against 
modifying the index.  It is set up to serve the Webpack bundle, as well and the index.

You should only need to add route files to `./server/routes` and models to `./server/models`.

### Client

The client contains all the React code.

All actions are stored inside of `./client/actions`.  I store action constants in here, so 
they're namespaced and easier to keep track of.  This is standard redux flow though, so check out
 [their docs](http://redux.js.org) for more.
 
The reducers are all stored inside of `./client/reducers`.  Similar to the routes and models, 
all reducers are required automatically and combined.  This way, sections of the state are 
automatically split by Redux's `combineReducers` function.  Additionally, I use [immutable.js](https://facebook.github.io/immutable-js/)
to ensure that state changes are reflected, and easy to reason about.  The project contains 
`redux-immutable` to allow all parts of the state to be Immutable objects.

CSS Modules with PostCSS is included.  There are [some](https://github.com/jonathantneal/postcss-short) 
[great](http://cssnext.io/) [resources](https://ismamz.github.io/postcss-utilities/docs) 
resources included by default, and you can find more at [PostCSS](https://github.com/postcss/postcss).

`./client/components/App.jsx` is a pure, stateless component, but you can do what you like to it.
Currently, it hot reloads, and all sub-components of it should also do it.  The console will warn
 you if you break hot reloading.
 
### Hot Reloading

At present, both sections of the project hot-reload.  The main motivation behind hot-reloading 
the server was so that the hot-reloading of the client was not broken by using something like 
nodemon.

The server-side hot-reloading is a tad hacky.  Essentially, what it does, it to soft-require, or 
require without assigning to a variable, all the routes (and subsequently all their sub-modules).
 When there is a change to any file inside of `./server`, the node require cache is invalidated, 
 so on the next request, all the modules are re-required, executing the new code.

#### Notes

This project, and most definitely this README is a work in progress.  I update it after almost 
every project I start from it, so I wouldn't go staking your life's work on it.  Almost every 
update is completely breaking.

It is a good place to start if you want to see how a project is set up.  It is also good if you 
want to see how you can incorporate hot-reloading into your existing project.