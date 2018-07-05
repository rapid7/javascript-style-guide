# NPM package

## Table of Contents

  1. [Structure](#structure)
  1. [Consumption](#consumption)
  1. [Publishing](#publishing)

## Structure

  - [1.1](#1.1) <a name='1.1'></a> **`src` folder**: Keep all source files in a top-level `src` folder

  > Why? Easily communicates where all the source files are located to future contributors.

  ```
  // bad
  my-package
  /files
    /js
      index.js

  // good
  my-package
  /src
    index.js
  ```
  - [1.2](#1.2) <a name='1.2'></a> **`test` folder**: Keep all test files in a top-level `test` folder that replicates the folder structure in `src`

  > Why? Easily communicates where all the test files are located to future contributors, and creates simple reconciliation to what source files they relate to.

  ```
  // bad
  my-package
  /src
    /utils
      foo.test.js
      foo.js
    index.test.js
    index.js

  // good
  my-package
  /src
    /utils
      foo.js
    index.js
  /test
    /utils
      foo.js
    index.js
  ```

  - [1.3](#1.3) <a name='1.3'></a> **`dist` folder**: Keep all compiled output files in a top-level `dist` folder

  > Why? Identifies what the distributed output of the sources files are.

  ```
  // bad
  my-package
  /src
    index.js
  bundle.js

  // good
  my-package
  /dist
    bundle.js
  /src
    index.js
  ```

  - [1.4](#1.4) <a name='1.4'></a> **`lib` folder**: Keep all transpiled files in a top-level `lib` folder

  > Why? Easily communicates where the consumed "library" files will be.

  ```
  // bad
  my-package
  /package
    index.js
  /src
    index.js

  // good
  my-package
  /lib
    index.js
  /src
    index.js
  ```

  - [1.5](#1.5) <a name='1.5'></a> **Configuration files**: Keep all configuration files at the top level

  > Why? Makes use of them easily inferred from their related packages.

  ```
  // bad
  my-package
  /babel
    .babelrc
  /eslint
    .eslintrc

  // good
  my-package
  .babelrc
  .eslintrc
  ```

## Consumption

  - [2.1](#2.1) <a name='2.1'></a> **Favor `lib` over `dist`**: Focus construction on consumption of transpiled files only

  > Why? Allows for more flexibility of consumption, as well as much smaller application bundle sizes.

  ```javascript
  // bad
  "main": "dist/bundle.js"

  // good
  "main": "lib/index.js"
  ```

  - [2.2](#2.2) <a name='2.2'></a> **Still include `dist` in publishes**: Give both options to the consumer

  > Why? Allows for even more flexibility of consumption, because why not?

  - [2.3](#2.3) <a name='2.3'></a> **Externalize as much as possible**: Keep the size of your `dist` bundle small

  > Why? Ensures that duplication of dependencies do not exist (could cause breakages), but also keeps consuming application bundle small.

  ```javascript
  // good
  externals: {
    react: {
      amd: 'react',
      commonjs2: 'react',
      commonjs: 'react',
      root: 'React'
    },
    'react-dom': {
      amd: 'react-dom',
      commonjs2: 'react-dom',
      commonjs: 'react-dom',
      root: 'ReactDOM'
    },
    ...etc
  }
  ```

## Publishing

  - [3.1](#3.1) <a name='3.1'></a> **Do not include unneeded files**: Only publish what is needed

  > Why? Keeps publish storage small (remember we store this internally!).

  Examples of files you should not include:
  * Configuration files (babel, eslint, webpack, etc)
  * Test files
  * Mocks
