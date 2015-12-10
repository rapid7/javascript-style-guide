# Rapid7 React/JSX Style Guide

*A mostly reasonable approach to React and JSX*

## Table of Contents

  1. [Basic Rules](#basic-rules)
  1. [ES2015 Syntax](#es2015-syntax)
  1. [Naming](#naming)
  1. [Declaration](#declaration)
  1. [PropTypes](#proptypes)
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
  
## ES2015 syntax

  - Use ES2015 syntax for all imports / exports
    
    ```javascript
    // bad
    var Dropdown = require('./Dropdown');
    
    // good
    import Dropdown from './Dropdown';
    
    // bad
    module.exports = React.createClass({
      ...
    });
    
    // good
    export default class Foo extends React.Component {
      ...
    }
    ```

  - Use class extends React.Component instead of React.createClass.

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
  
  - Always give the class a name
  
  ```javascript
  // bad
  class extends React.Component {
    ...
  }
  
  // good
  class Foo extends React.Component {
    ...
  }
  ```

## Naming

  - **Extensions**: Use `.jsx` extension for React components.
  - **Filename**: Use PascalCase for filenames. E.g., `ReservationCard.jsx`.
  - **Reference Naming**: Use PascalCase for React components and camelCase for their instances:
    ```javascript
    // bad
    import reservationCard from './ReservationCard'

    // good
    import ReservationCard from './ReservationCard'

    // bad
    const ReservationItem = <ReservationCard />;

    // good
    const reservationItem = <ReservationCard />;
    ```

    **Component Naming**: Use the filename as the component name. For example, `ReservationCard.jsx` should have a reference name of `ReservationCard`.
    ```javascript
    // bad
    import Footer = from './Footer/index'

    // good
    const Footer = require('./Footer')
    ```
    
    **Dedicated Sub-Component Naming**: Namespace them in a separate folder with the camelCase name of the component and include the component name as a prefix to all sub-components.
    ```javascript
    // bad
    import Footer from './Footer';
    import FooterContact from './Contact';
    
    // good
    import Footer from './Footer';
    import FooterContact from './footer/FooterContact';
    ```
    
    > Why? Allows for easy searching of related files both visually and from an IDE search filename perspective.

## Declaration
  - Always create the displayName property for components, and have it match the class name.

    ```javascript
    // bad
    export default class ReservationCard extends React.Component {
      // stuff goes here
    });

    // good
    export default class ReservationCard extends React.Component {
        displayName = 'ReservationCard';
        // stuff goes here
    }
    ```
    
## PropTypes
  - Always declare all PropTypes. For non-required props that are not functions, provide defaults.
  
    ```javascript
    // bad
    class Foo extends React.Component {
        render() {
            return (
                <div id={this.props.id}>
                    {this.props.name}
                </div>
            );
        }
    }
    
    // good
    class Foo extends React.Component {
        static propTypes = {
            id:React.PropTypes.string.isRequired,
            name:React.PropTypes.string
        };
        
        static defaultProps = {
            name:"Billy"
        };
        
        render() {
            return (
                <div id={this.props.id}>
                    {this.props.name}
                </div>
            );
        }
    }
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
      anotherSuperLongParam="baz"
    />
    
    // also good
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz">
      Bar
    </Foo>

    // if props fit in one line then keep it on the same line
    <Foo bar="bar" />

    // children get indented normally
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz">
      <Spazz />
    </Foo>
    ```

## Quotes
  - Always use double quotes (`"`) for JSX attributes, but single quotes for all other JS.

  > Why? JSX attributes [can't contain escaped quotes](http://eslint.org/docs/rules/jsx-quotes), so double quotes make conjunctions like `"don't"` easier to type.
  > Regular HTML attributes also typically use double quotes instead of single, so JSX attributes mirror this convention.

    ```javascript
    // bad
    <Foo bar='bar' />

    // good
    <Foo bar="bar" />

    // bad
    <Foo style={{ left: "20px" }} />

    // good
    <Foo style={{ left: '20px' }} />
    ```

## Spacing
  - Always include a single space in your self-closing tag.
  
    ```javascript
    // bad
    <Foo/>

    // very bad
    <Foo                 />

    // bad
    <Foo
     />

    // good
    <Foo />
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
  
  - Always sort the prop names alphabetically
  
    ```javascript
    // bad
    <Foo
      userName="hello"
      phoneNumber={12345678}
    />
    
    // good
    <Foo
      phoneNumber={12345678}
      userName="hello"
    />
    ```  

## Parentheses
  - Wrap JSX tags in parentheses:
  
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
    ```
  > Why? All tags can now align with their respective closing tags when indented without extra whitespace.

## Tags
  - Always self-close tags that have no children.
    ```javascript
    // bad
    <Foo className="stuff"></Foo>

    // good
    <Foo className="stuff" />
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
    class Foo extends React.Component {
      _onClickSubmit() {
        // do stuff
      }

      // other stuff
    });

    // good
    class Foo extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    });
    ```

## Ordering

  - Ordering for class extends React.Component:
  
  1. displayName
  1. propTypes
  1. contextTypes
  1. childContextTypes
  1. defaultProps
  1. constructor
  1. getDefaultProps
  1. getInitialState
  1. state
  1. getChildContext
  1. componentWillMount
  1. componentDidMount
  1. componentWillReceiveProps
  1. shouldComponentUpdate
  1. componentWillUpdate
  1. componentDidUpdate
  1. componentWillUnmount
  1. optional static properties (alphabetized)
  1. optional instance properties (alphabetized)
  1. optional static methods (alphabetized)
  1. optional instance methods (alphabetized)
  1. render

  - How to define propTypes, defaultProps, contextTypes, etc...  

  ```javascript
  import React from 'react';
  
  export default class Link extends Component {
    displayName = 'Link';
    
    static propTypes = {
        id: React.PropTypes.number.isRequired,
        url: React.PropTypes.string.isRequired,
        text: React.PropTypes.string,
    };
    
    static defaultProps = {
        text: 'Hello World',
    };
  
    static methodsAreOk() {
      return true;
    }
  
    render() {
      return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
    }
  }
  ```

**[⬆ back to top](#table-of-contents)**
