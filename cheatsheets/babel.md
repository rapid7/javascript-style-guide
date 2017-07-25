# Babel

## Table of Contents

  1. [Presets](#presets)
  1. [Plugins](#plugins)

## Presets

  - [1.1](#1.1) <a name='1.1'></a> **babel-preset-env**: Make use of environment-specific babel configuration instead of spec-based.

  > Why? Will allow us to continually define our "supported environments" as they change without needing to update the preset.

  ```javascript
  // .babelrc configuration
  "presets": [
    ["env", {
      "loose": true
    }]
  ]

  // package.json configuration
  "browserslist": [
    "last 2 versions",
    "ie 11"
  ]
  ```

  Please note that this requires setup in both your `.babelrc` and `package.json`

  - [1.2](#1.2) <a name='1.2'></a> **babel-preset-stage-2**

  > Why? Allows for likely future additions to the spec to be coded today.
  
  > Why stage 2? Early enough in the process that it will not be included for some time, however the likelihood of the proposal is very high because the draft has been accepted.

  ```javascript
  "presets": [
    "stage-2"
  ]
  ```

  - [1.3](#1.3) <a name='1.3'></a> **babel-preset-react**

  > Why? Needed for any form of `react` development.

  ```javascript
  "presets": [
    "react"
  ]
  ```

  Please note that this is only required if doing development involving `React` components.

## Plugins

The plugins to include is highly-dependent on the package / application you are building, so no specific plugins are advised across all projects. That said, there are some plugins meant for specific use cases that can be recommended.

  - [2.1](#2.1) <a name='2.1'></a> **babel-plugin-transform-runtime**: Handles conversion of generators and `async` functions.

  > Why? Needed if your package performs asynchronous programming, such as the use of API calls.

  ```javascript
  "plugins": [
    "transform-runtime"
  ]
  ```

  - [2.2](#2.2) <a name='2.2'></a> **babel-plugin-add-react-displayname**: Automatically adds the state displayName property to all class and functional react components.

  > Why? Helpful in production to prevent minified code from having mangled component names.

  ```javascript
  "plugins": [
    "add-react-displayname"
  ]
  ```
  
  - [2.3](#2.3) <a name='2.3'></a> **babel-plugin-transform-decorators-legacy**: Handles conversion of the `@decorator` syntax.

  > Why? Needed if you consider the syntax of greater convenience over the standard function wrapping technique.

  ```javascript
  "plugins": [
    "transform-decorators-legacy"
  ]
  ```

  - [2.4](#2.4) <a name='2.4'></a> **babel-plugin-add-module-exports**: Adds automatic conversion of files that use ES2015 `export` syntax with only a default export to also include `module.exports` assignment.

  > Why? If your package combines ES2015 syntax with CommonJS syntax for imports / requires, this allows you to use them interchangeably.

  ```javascript
  "plugins": [
    "add-module-exports"
  ]
  ```

  - [2.5](#2.5) <a name='2.5'></a> **babel-plugin-transform-react-remove-prop-types**: Removes assignment of `prop-types` from component declarations for `react`.

  > Why? If your package is a standalone application (not a consumed package) and includes the use of `react` it is advised that production builds strip prop types for filesize reasons.

  ```javascript
  "env": {
    "production": {
      "plugins": [
        "transform-react-remove-prop-types"
      ]
    }
  }
  ```
