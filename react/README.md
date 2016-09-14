# Rapid7 React/JSX Style Guide

*A mostly reasonable approach to React and JSX*

## Table of Contents

  1. [Basic Rules](#basic-rules)
  1. [Naming](#naming)
  1. [Declaration](#declaration)
  1. [Alignment](#alignment)
  1. [Quotes](#quotes)
  1. [Spacing](#spacing)
  1. [Props](#props)
  1. [Parentheses](#parentheses)
  1. [Tags](#tags)
  1. [Methods](#methods)
  1. [Ordering](#ordering)

## Basic Rules

  - Only include one React component per file.
  - Always use JSX syntax.
  - Do not use `React.createElement` unless you're initializing the app from a file that is not JSX.

## Class vs React.createClass

  - Use class extends React.Component unless you have a very good reason to use mixins.

  ```javascript
  // bad
  const Listing = React.createClass({
    render() {
      return <div />;
    }
  });

  // good
  class Listing extends React.Component {
    render() {
      return <div />;
    }
  }
  ```

## Naming

  - **Extensions**: Use `.js` extension for React components.
  - **Filename**: Use PascalCase for filenames. E.g., `ReservationCard.js`.
  - **Reference Naming**: Use PascalCase for React components and camelCase for their instances:
    ```javascript
    // bad
    const reservationCard = require('./ReservationCard');

    // good
    const ReservationCard = require('./ReservationCard');

    // bad
    const ReservationItem = <ReservationCard />;

    // good
    const reservationItem = <ReservationCard />;
    ```

    **Component Naming**: Use the filename as the component name. For example, `ReservationCard.js` should have a reference name of `ReservationCard`. However, for root components of a directory, use `index.js` as the filename and use the directory name as the component name:
    ```javascript
    // bad
    const Footer = require('./Footer/Footer.js')

    // bad
    const Footer = require('./Footer/index.js')

    // good
    const Footer = require('./Footer')
    ```


## Declaration
  - Do not use displayName for naming components. Instead, name the component by reference.

    ```javascript
    // bad
    export default React.createClass({
      displayName: 'ReservationCard',
      // stuff goes here
    });

    // good
    export default class ReservationCard extends React.Component {
    }
    
    // good
    class ReservationCard extends React.Component {
    }
    
    export default ReservationCard;
    ```

## Alignment
  - Follow these alignment styles for JSX syntax

    ```javascript
    // bad
    <Foo superLongParam="bar"
         anotherSuperLongParam="baz" />

    // good
    <Foo
      superLongParam="bar"
      superLongParamAlso="baz"
    />

    // if props fit in one line then keep it on the same line
    <Foo bar="bar"/>

    // children get indented normally
    <Foo
      superLongParam="bar"
      superLongParamAlso="baz"
    >
      <Spazz/>
    </Foo>
    ```

## Quotes
  - Always use double quotes (`"`) for JSX attributes, but single quotes for all other JS.

  > Why? JSX attributes [can't contain escaped quotes](http://eslint.org/docs/rules/jsx-quotes), so double quotes make conjunctions like `"don't"` easier to type.
  > Regular HTML attributes also typically use double quotes instead of single, so JSX attributes mirror this convention.

    ```javascript
    // bad
    <Foo bar='bar'/>

    // good
    <Foo bar="bar"/>
    ```

## Spacing
  - Always remove ending space in your self-closing tag.
    ```javascript
    // bad
    <Foo/>

    // very bad
    <Foo                 />

    // bad
    <Foo
     />

    // bad
    <Foo />

    // good
    <Foo/>
    ```

## Props
  - Always use camelCase for prop names.
    ```javascript
    // bad
    <Foo
      phone_number={12345678}
      UserName="hello"
    />

    // good
    <Foo
      phoneNumber={12345678}
      userName="hello"
    />
    ```

## Parentheses
  - Wrap JSX tags in parentheses when returned:
    ```javascript
    /// bad
    render() {
      return <MyComponent className="long body" foo="bar">
               <MyChild />
             </MyComponent>;
    }

    // good
    render() {
      return (
        <MyComponent className="long body" foo="bar">
          <MyChild />
        </MyComponent>
      );
    }

    // good
    render() {
      const body = <div>hello</div>;
      
      return (
        <MyComponent>
          {body}
        </MyComponent>
      );
    }
    ```

## Tags
  - Always self-close tags that have no children.
    ```javascript
    // bad
    <Foo className="stuff"></Foo>

    // good
    <Foo className="stuff"/>
    ```

  - If your component has multi-line properties, close its tag on a new line.
    ```javascript
    // bad
    <Foo
      bar="bar"
      baz="baz" />

    // good
    <Foo
      bar="bar"
      baz="baz"
    />
    ```

## Methods
  - Do not use underscore prefix for internal methods of a React component.
    ```javascript
    // bad
    React.createClass({
      _onClickSubmit() {
        // do stuff
      }

      // other stuff
    });

    // good
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    });
    ```

## Ordering

  - Ordering for class extends React.Component:

  1. propTypes
  2. defaultProps
  3. contextTypes
  4. constructor
  5. optional static methods
  6. getChildContext
  7. componentWillMount
  8. componentDidMount
  9. componentWillReceiveProps
  10. shouldComponentUpdate
  11. componentWillUpdate
  12. componentDidUpdate
  13. componentWillUnmount
  14. *clickHandlers or eventHandlers* like onClickSubmit() or onChangeDescription()
  15. *getter methods for render* like getSelectReason() or getFooterContent()
  16. *Optional render methods* like renderNavigation() or renderProfilePicture()
  17. render

  - How to define propTypes, defaultProps, contextTypes, etc...

  ```javascript
  import React, { 
    Component, 
    PropTypes 
  } from 'react';

  class Link extends Component {
    static propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string
    };
    
    static defaultProps = {
      text: 'Hello World'
    };
  
    static methodsAreOk() {
      return true;
    }

    render() {
      return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
    }
  }

  export default Link;
  ```

**[⬆ back to top](#table-of-contents)**
