# Welcome to JavaScript at Rapid7!

* [Setting up npm](#setting-up-npm)
  * [Environment](#envionment)
  * [Initialize your application](#initialize-your-application)
  * [Packages](#packages)
* [Setting up Webpack](#setting-up-webpack)
  * [Webpack configuration](#webpack-configuration)
* [Setting up Babel](#setting-up-babel)
  * [Babel configuration](#babel-configuration)
  * [Integrating Babel with Webpack](#integrating-babel-with-webpack)
* [Setting up ESLint](#setting-up-eslint)
  * [ESLint configuration](#eslint-configuration)
  * [Integrating ESLint with Webpack](#integrating-eslint-with-webpack)
* [Setting up Webpack … for dev](#setting-up-webpack-for-dev)
  * [Webpack dev configuration](#webpack-dev-configuration)
  * [Add an npm script](#add-an-npm-script)
* [Developing your app](#developing-your-app)
  * [Folder structure](#folder-structure)
  * [Setting up React](#setting-up-react)
* [Adding Rapid7 styles](#adding-rapid7-styles)
  * [Install related dependencies](#install-related-dependencies)
  * [Add the styles](#add-the-styles)
  

npm is the crux of all modern JavaScript development … I won't begin to describe it because I will not do it justice, but if you are unfamiliar with it I advise you learn because without it … we are all lost.

## Setting up npm

#### Environment

Naturally we use the standard npm infrastructure for all external packages, however we also have a variety of internal packages that are not available through the standard npm repositories. Instead, we proxy the public npm repository internally and then augment it with these packages. To access this internal repository, you need to set up your .npmrc file to point to it:

```
registry=https://artifacts.bos.rapid7.com/nexus/content/groups/npm-all/
```

A heads up, there are some organization-specific repositories that are sub-dependencies of packages that we use which have the "@" leading character, which our internal repository does not like. As such, we need to specify these repositories individually. An example of required repos using AVA (our testing library of choice):

```
@ava:registry=https://registry.npmjs.org/
@concordance:registry=https://registry.npmjs.org/
@types:registry=https://registry.npmjs.org/
```

Naturally, add them as you see fit.

#### Initialize your application

If you run the following command in the terminal, it will create a package.json file that will contain metadata about your application (and will be used heavily for package and script management):

```
$ npm init
```

There are a series of interactive questions that follow, and feel free to answer as you see fit for your app. If the defaults make sense, just hit enter.

#### Packages

Now that you have npm pointing to the correct place, you can install your packages. This list may seem long, but it is the bare minimum necessary to get a React + Redux application running with Webpack and our current standards (trust me, you will likely add many more).

* dependencies *npm install [package] --save*
  * prop-types
  * react
  * react-dom
* devDependencies *npm install [package] --save-dev*
  * babel-cli
  * babel-eslint
  * babel-loader
  * babel-plugin-transform-runtime
  * babel-polyfill
  * babel-preset-env
  * babel-preset-react
  * babel-preset-stage-2
  * babel-runtime
  * eslint
  * eslint-config-rapid7
  * eslint-friendly-formatter
  * eslint-plugin-babel
  * html-webpack-plugin
  * webpack
  * webpack-dev-server
  
I'll cover more about additional packages that you'll need when I cover the context they are installed in.

## Setting up Webpack

Webpack can be very confusing; in fact, in version one the single largest complaint was that setting up a configuration could take a person days. Even now, when the documentation has gotten much better, we are learning new things that have existed for ages that can improve our setup. Long story short, do not feel like you are crazy if it takes a while to sink in.

#### Webpack configuration

A good methodology is to start with a basic configuration, and extend it in a different file for use-case-specific contexts. An example of a simple configuration file:

```javascript
const path = require('path');
const webpack = require('webpack');

module.exports = {
  devtool: '#source-map',
  entry: './src/index.js',
  module: {
    rules: []
  },
  output: {
    filename: 'my-app.js',
    path: path.join(__dirname, 'dist')
  }
};
```

This doesn't look too crazy, but that is because the rules are not populated (yet). Rules are the crux of webpack, as they are what allow it to load non- JavaScript files as if they were JavaScript files. This will allow you import CSS, images, etc. like any other file. Quite useful! We shall cover the rules population in later sections, when they are contextually applicable.

For now, let's save this at the top level of our app as webpack.config.js.

## Setting up Babel

Babel is a transpilation engine, meaning it compiles from a language into the same language in a normalized format.

#### Babel configuration

The babel configuration is basically a JSON file, and it always resides on the top-level of your application as .babelrc. An example of a bare-bones configuration based on the packages installed above:

```javascript
{
  "plugins": [
    "transform-runtime"
  ],
  "presets": [
    ["env": {
      "loose": true
    }],
    "react",
    "stage-2"
  ]
}
```

This will be automatically applied once we set up webpack to consume it, so we are basically done for now. As we add more plugins or presets, you simply add them to this configuration.

#### Integrating Babel with Webpack

Our first rule! The babel rule is the most common, naturally because we want Babel to pre-process all our JavaScript code prior to it being rendered. This will allow us to write with the latest hotness but still work in all browsers.

Let's modify our rules in our webpack config to be this:

```javascript
rules: [
  {
    include: [
      path.resolve(__dirname, 'src')
    ],
    loader: 'babel-loader',
    test: /\.js$/
  }
]
```

Pretty straightforward, we are saying all files that exist in src should be processed with the babel-loader. We can get more extravagant with the options if needed, but for now this will be totally sufficient.

## Setting up ESLint

ESLint is a code format and syntax validation tool. The single largest source of bikeshedding within a development team is code style, and with JavaScript there are 10,000 ways to do any 1 thing. As such, we developed a myriad of rules that define code format and syntax preferences to ensure that our codebase remains consistent. While it takes all developers some time to get used to it, over the long term it saves a lot of time because any developer can step into a new codebase and recognize patterns, hopefully letting them hit the ground running.

#### ESLint configuration

We have extrapolated out all of Rapid7's ESLint rules into a single (open-sourced) package, eslint-config-rapid7. First, we should install it:

```
npm install eslint-config-rapid7 --save-dev
```

Then we can set up the configuration file, which (like the .babelrc file) lives in the top level as .eslintrc as a JSON file. A simple example:

```javascript
{
  "extends": "rapid7/browser",
  "globals": {},
  "parser": "babel-eslint",
  "parserOptions": {
    "ecmaVersion": 2017,
    "sourceType": "module"
  },
  "rules": {}
}
```

The "extends" field will pull the rules from the Rapid7 browser-specific configuration, which is most of the heavy lifting. The "parser" says that we are going to pre-process the code with Babel prior to ESLint performing its validation. "parserOptions" are just saying we are using the latest ECMAScript version, and that we are using browser modules.

The two things you may be curious about are the empty objects for "globals" and "rules". "globals" are pretty straightforward, in that if you define a key in there, that key will be allowed as a "global" variable (which is disallowed normally with ESLint). Example:

```javascript
"globals": {
  "$": true
}
```

If for some reason you needed jQuery to be a global variable instead of scoped, this is how it would be defined.

The "rules" section is to provide any additional rules on top of the ones provided by the Rapid7 config. I'll defer to the ESLint documentation on setting up rules if you want to pursue this option.

#### Integrating ESLint with Webpack

Another rule! This one requires another package that we installed before, so I'll give that require as well:

```javascript
rules: [
  {
    enforce: 'pre',
    include: [
      path.resolve(__dirname, 'src')
    ],
    loader: 'eslint-loader',
    options: {
      configFile: '.eslintrc',
      formatter: require('eslint-friendly-formatter')
    },
    test: /\.js$/
  },
  ...otherRules
]
```

Similar to the rule we set up for Babel, but with a couple of additional properties. First, the "enforce" property will ensure that this rule is run before the other rules (which, if we have a syntax error, we want to know right away!). The "options" object is a list of options that we pass to the loader, in this case to say where the configuration file lives and to use the friendly formatter that displays error messages in a pretty format.

## Setting up Webpack … for dev

Wait, I already set up my configuration, didn't I?

Yes, but you set up webpack to build your files, which will work from the command line but not for regular development in-browser. For that, we need to do a little more.

#### Webpack dev configuration

Let's set up another file on the top level of the app that is named webpack.config.dev.js, and this will be the configuration file specific to development. It's contents:

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const defaultConfig = require('./webpack.config.js');

module.exports = Object.assign({}, defaultConfig, {
  devServer: {
    contentBase: './dist',
    host: 'localhost',
    port: 3000
  },
  plugins: [
    ...defaultConfig.plugins,
    new HtmlWebpackPlugin()
  ]
});
```

This small extension does two things. First, it sets up a development server that will listen for changes to files and re-build your code for you in the background. This makes development very speedy, and it even refreshes the browser so that we can see our changes immediately. Second, the HtmlWebpackPlugin will spin up a dummy index.html page, whose sole purpose in life is to serve up the JavaScript app that we are building in our src directory.

#### Add an npm script

In our package.json, there is a "scripts" section, so we can add in an entry as such:

```javascript
{
  "dev": "NODE_ENV=development webpack-dev-server --config=webpack.config.dev.js"
}
```

This script will call the dev server with the appropriate configuration file, starting up our app.

## Developing your app

Now that we have the infrastructure in place, let's make magic happen!

#### Folder structure

Our standards dictate that all source files live in a "src" folder at the top level. You probably noticed our infrastructure was being build with this in mind. You may also have noticed that we were using "dist" as the base of our files, because that is the standard destination folder for the built code. In case you are curious, all test files to in a "test" folder.

So, create a folder named "src" in the top level, then create a file inside of it named "index.js". Let's start with some basic contents:

```javascript
console.log('Hello world!');
```

Now if you run the dev script:

```
$ npm run dev
```

Your application will build, and once complete you can go to localhost:3000 to see your app. It will be a glorious blank page, but if you look in the Console in the browser dev tools you should see "Hello world!".

#### Setting up React

There are quite literally thousands of tutorials for React out there, and depending on the context of your application it could go a bunch of directions regarding package dependencies, requirements, etc. So … I will go super basic, and you can take it from there.

An example of the simplest React app that could possibly exist:

```javascript
import React, {PureComponent} from 'react';
import {render} from 'react-dom';

class App extends PureComponent {
  render() {
    return (
      <div>
        Hello, world!
      </div>
    );
  }
}

render(<App/>, document.body);
```

Now, React will bark at you because it does not like to be added to document.body directly. Basically, the second parameter in render is the element to render inside of, and choosing document.body replaces your entire body content … which is likely not a good thing! We can remedy this with some basic JavaScript magic:

```javascript
const div = document.createElement('div');

render(<App/>, div);

document.body.appendChild(div);
```

This creates a child element for React to render inside of, so it is happy. Now you have a full-fledged React app!

## Adding Rapid7 styles

The simplest integration of Rapid7-specific items are to integrate the styles, which also gives us a chance to add some common rules to webpack.

#### Install related dependencies

Rapid7 styles are not actually styles, but rather a collection of Sass mixins that you can apply to your project as you see fit. To bring them in to your application, you need the following dependencies:

* dependencies
  * rapid7-styles
* devDependencies
  * css-loader
  * node-sass
  * postcss-cssnext
  * postcss-loader
  * resolve-url-loader
  * sass-loader
  * style-loader
  
Lot's of dev dependencies, but basically each loader is a cascading handler of a specific thing ... `sass-loader` uses `node-sass` to compile the sass files to a format that `css-loader` expects, which builds standard CSS into a string that `style-loader` can use to inject into the DOM. `postcss-loader` is present to provide prefixes and such for older browsers ... think of it as Babel for CSS.

Once you've installed those packages, you can add the following rule to your webpack.config.js file:

```javascript
const cssnext = require('postcss-cssnext');
...
rules: [
  ...restOfRules,
  {
    include: [
      /node_modules/,
      path.resolve(__dirname, 'src')
    ],
    use: [
      {loader: 'style-loader},
      {
        loader: 'css-loader',
        options: {
          sourceMap: true
        }
      },
      {
        loader: 'postcss-loader,
        options: {
          plugins() {
            return [
              cssnext()
            ];
          },
          sourceMap: true
        }
      },
      {loader: 'resolve-url-loader'},
      {
        loader: 'sass-loader',
        options: {
          includePaths: [
            path.resolve(__dirname, 'node_modules', 'foundation-sites', 'scss'),
            path.resolve(__dirname, 'node_modules', 'rapid7-styles'),
            path.resolve(__dirname, 'node_modules', 'rapid7-styles', 'styles', 'scss')
          ],
          sourceMap: true
        }
      }
    ]
  }
]
```

Welcome to the chainability of Webpack rules ... the "use" property accepts an array of loader objects, which starts with the last and feeds into the preceeding one in order. Most of the options individually should be understandable, save for the "includePaths" for the Sass files ... that is a very specific requirement for Rapid7 styles, as it expects certain include paths to be present in the environment it runs in.

#### Add the styles

First, create a Sass file in "src" named "App.scss" that brings in the mixins and applies them:

```sass
@import '~rapid7-styles';

@include r7-cards;
```

This example brings in the card styles, but you can apply the mixins however you see fit. More information is available on the [documentation site](http://styles.rapid7.com/). If you are curious about the `~` prior to the import, that is magic provided by the `sass-loader` ... it will resolve the package dependency for you, rather than needing to provide a strict reference to it. This is the preferred way to handle Sass package imports, as relative referencing is inherently brittle.

Then, to add the styles to your app, you can just import the file like you would a JavaScript file. Because it is simply adding the CSS to the dom (not using a variable or anything), it does not need to be assigned to an import.

```javascript
import React, {PureComponent} from 'react';
import {render} from 'react-dom';

// styles
import './App.scss';
```

This will make the class names available to you, as the built CSS from `App.scss` has been injected into the DOM.
