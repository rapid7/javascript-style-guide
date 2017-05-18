# Component API

## Table of Contents

  1. [Philosophy](#philosophy)
  1. [Props](#props)
  1. [State](#state)
  1. [Context](#context)

## Philosophy

  - [1.1](#1.1) <a name='1.1'></a> **Keep the scope targeted**: Components should do one thing and do it well

  > Why? Keeping the scope small allows for rapid implementation, easy testing, and far less convoluted documentation. It is very cheap to build another simple component, and very expensive later to debug a complex one.

  - [1.2](#1.2) <a name='1.2'></a> **For consumed packages, favor simplicity in consumption, not construction**: When building a package that is consumed by a parent application, focus more about how the component will be consumed

  > Why? The DX (developer experience) for a component API is a major part of adoption, and by thinking of the consumer first it will drive design towards a universal and simple interface.

## Props

  - [2.1](#2.1) <a name='2.1'></a> **Explicit names**: Make your prop names specific to their application and context

  > Why? Gives more detail to the consumer about what should populate it, and also how it is used internally.

  ```javascript
  // bad
  <MyComponent token={csrfToken}/>

  // good
  <MyComponent csrfToken={csrfToken}/>
  ```

  - [2.2](#2.2) <a name='2.2'></a> **Favor passing separately for required props**: Props that are required should be passed individually instead of combined into an object

  > Why? Identifies component requirements explicitly without needing to visit the documentation each time there is an update of values

  ```javascript
  // bad
  <MyComponent
    options={{
      foo: 'foo',
      bar: 'bar'
    }}
  />

  // good
  <MyComponent
    foo="foo"
    bar="bar"
  />
  ```

## State

  - [3.1](#3.1) <a name='3.1'></a> **If building a consumable package, favor internal state**: If your component will be consumed by a parent application, avoid the need for a specific state management solution if possible.

  > Why? Creates more flexibility in what applications can consume your package.

## Context

  - [4.1](#4.1) <a name='4.1'></a> **If building a standalone application, limit it's use**: Choose the values you put in context carefully

  > Why? It is considered bad practice for every component to know everything, instead favor passing values as props, or storing values in app-level state and connecting your component.

  - [4.2](#4.2) <a name='4.2'></a> **If building a consumable package, don't do it**: If your component will be consumed by a parent application, it should use props instead

  > Why? Creates unnecessary tight-coupling that is implicit, and passing the value through props is very cheap.
