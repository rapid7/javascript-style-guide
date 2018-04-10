# Immutable

## Table of Contents

  1. [Arrays](#arrays)

## Arrays

  - [1.1](#1.1) <a name='1.1'></a> **Adding to an array**: Prefer use of spread operator instead of push or direct assignment to add items to an array.

  ```javascript
  // bad
  const length = items.length;
  const itemsCopy = [];
  
  let index = -1;
  
  while (++index < length) {
    itemsCopy[index] = items[index];
  }
  
  // good
  const itemsCopy = [...items];
  ```

  - [1.2](#1.2) <a name='1.2'></a> **Copying Arrays**: Use the spread operator ... to copy arrays.

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

  - [1.3](#1.3) <a name='1.3'></a> **Convert Array like Objects**: To convert an array-like object to an array, use the spread operator.

    ```javascript
    const nodes = document.querySelectorAll('.foo');
    const arrayOfNodes = [...nodes];
    ```

