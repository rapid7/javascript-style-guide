# Redux

## Table of Contents

  1. [All actions](#redux-actions)
  1. [Redux actions](#redux-actions)
  1. [Manual synchronous actions](#manual-synchronous-actions)
  1. [Manual asynchronous actions](#manual-asynchronous-actions)
  1. [Redux actions reducers](#redux-actions-reducers)
  1. [Manual reducers](#manual-reducers)

## All actions

  - [1.1](#1.1) <a name='1.1'></a> **Namespace actions by filename**: Provide a namespace for all action types

  > Why? Prevents the possibility of action collisions.

  ```javascript
  // bad
  export const ACTION_TYPES = {
    LOAD: 'LOAD'
  };

  // good
  const ACTION_TYPES_NAMESPACE = 'userData';

  export const ACTION_TYPES = {
    LOAD: `${ACTION_TYPES_NAMESPACE}:LOAD`
  };
  ```

  It is also recommended to use the [namespace-constants](https://www.npmjs.com/package/namespace-constants) package, as it provides a consolidated interface to accomplish this same task.

  ```javascript
  import createConstants from 'namespace-constants';

  export const ACTION_TYPES = createConstants('userData', [
    'LOAD'
  ]);
  ```

## Redux actions

  - [2.1](#2.1) <a name='2.1'></a> **Use redux-actions if possible**: Prefer redux-actions over manually building your own actions.

  > Why? Builds FSA-compliant actions with a consistent API, making reducer handling much easier. Also, eliminates the need to test simple actions.

  Synchronous example:

  ```javascript
  import {
    createAction
  } from 'redux-actions';

  const ACTION_TYPES_PREFIX = 'stuff';

  export const ACTION_TYPES = {
    ASSIGN: `${ACTION_TYPES_PREFIX}:ASSIGN`
  };

  export const assignStuff = createAction(ACTION_TYPES.ASSIGN);
  ```

  Asynchronous example:

  ```javascript
  import {
    createAction
  } from 'redux-actions';

  const ACTION_TYPES_PREFIX = 'stuff';

  export const ACTION_TYPES = {
    GET_FAIL: `${ACTION_TYPES_PREFIX}:GET_FAIL`,
    GET_PENDING: `${ACTION_TYPES_PREFIX}:GET_PENDING`,
    GET_SUCCESS: `${ACTION_TYPES_PREFIX}:GET_SUCCESS`
  };

  export const getStuffFail = createAction(ACTION_TYPES.GET_FAIL);
  export const getStuffPending = createAction(ACTION_TYPES.GET_PENDING);
  export const getStuffSuccess = createAction(ACTION_TYPES.GET_SUCCESS);

  export const loadStuff = () => {
    return async (dispatch) => {
      dispatch(getStuffPending());

      try {
        const data = await getStuff();

        dispatch(getStuffSuccess(data));
      } catch (error) {
        dispatch(getStuffFail(error));
      }
    };
  };
  ```

  - [2.2](#2.2) <a name='2.2'></a> **Place API calls in separate file**: Do not make API calls within the action, instead call a function imported from an API file.

  > Why? Ensures that your action creators remain targeted in scope, and also make them much easier to test.

  ```javascript
  // bad
  const loadStuff = () => {
    return async (dispatch) => {
      dispatch(getStuffPending());

      try {
        const {
          data
        } = await axios().get('/api/1/stuff');

        dispatch(getStuffSuccess(data));
      } catch (error) {
        dispatch(getStuffFail(error));
      }
    };
  };

  // good
  export const loadStuff = () => {
    return async (dispatch) => {
      dispatch(getStuffPending());

      try {
        const data = await getStuff();

        dispatch(getStuffSuccess(data));
      } catch (error) {
        dispatch(getStuffFail(error));
      }
    };
  };
  ```

## Manual synchronous actions

  - [3.1](#3.1) <a name='3.1'></a> **Follow FSA standards**: Follow [Flux Standard Action](https://github.com/acdlite/flux-standard-action) guidelines for building actions.

  > Why? Ensures a consistent interface when handling in the reducer

  ```javascript
  const ACTION_TYPES_PREFIX = 'stuff';

  export const ACTION_TYPES = {
    ASSIGN: `${ACTION_TYPES_PREFIX}:ASSIGN`
  };

  // bad
  export const assignStuff = (data) => {
    return {
      action: ACTION_TYPES.ASSIGN,
      data
    };
  };

  // good
  export const assignStuff = (data) => {
    return {
      payload: data,
      type: ACTION_TYPES.ASSIGN
    };
  };
  ```

  - [3.2](#3.2) <a name='3.2'></a> **Add error boolean for all error payloads**: Add the `error: true` property for all payloads that are errors.

  > Why? Easily identifies what is an error without needing to introspect the payload.

  ```javascript
  const ACTION_TYPES_PREFIX = 'stuff';

  export const ACTION_TYPES = {
    SEND_ERROR: `${ACTION_TYPES_PREFIX}:SEND_ERROR`
  };

  // bad
  export const sendError = (error) => {
    return {
      payload: error,
      type: ACTION_TYPES.SEND_ERROR
    };
  };

  // good
  export const sendError = (error) => {
    return {
      error: true,
      payload: error,
      type: ACTION_TYPES.SEND_ERROR
    };
  };
  ```

## Manual asynchronous actions

  - [4.1](#4.1) <a name='3.1'></a> **Use separate action types for async call stages**: Prefer specific action types for each stage of async call instead of single action type.

  > Why? Is more explicit tracking in redux dev tools, but also allows for simpler handling in the reducer and simpler testing.

  ```javascript
  const ACTION_TYPES_PREFIX = 'stuff';

  // bad
  export const ACTION_TYPES = {
    GET_STUFF: 'GET_STUFF'
  };

  export const getStuff = () => {
    return async (dispatch) => {
      dispatch({
        type: ACTION_TYPES.GET_STUFF
      });

      try {
        const data = await getStuff();

        dispatch({
          payload: data,
          type: ACTION_TYPES.GET_STUFF
        });
      } catch (error) {
        dispatch({
          error: true,
          payload: error,
          type: ACTION_TYPES.GET_STUFF
        })
      }
    };
  };

  // good
  export const ACTION_TYPES = {
    GET_FAIL: `${ACTION_TYPES_PREFIX}:GET_FAIL`,
    GET_PENDING: `${ACTION_TYPES_PREFIX}:GET_PENDING`,
    GET_SUCCESS: `${ACTION_TYPES_PREFIX}:GET_SUCCESS`
  };

  export const getStuff = () => {
    return async (dispatch) => {
      dispatch({
        type: ACTION_TYPES.GET_STUFF_PENDING
      });

      try {
        const data = await getStuff();

        dispatch({
          payload: data,
          type: ACTION_TYPES.GET_STUFF_SUCCESS
        });
      } catch (error) {
        dispatch({
          error: true,
          payload: error,
          type: ACTION_TYPES.GET_STUFF_FAIL
        })
      }
    };
  };
  ```

  - [4.2](#4.2) <a name='4.2'></a> **Handle all errors**: Handle both the success and error results of functions that can potentially cause errors, such as API calls.

  > Why? Ensures no runtime errors for the user.

  ```javascript
  // bad
  export const getStuff = () => {
    return async (dispatch) => {
      dispatch({
        type: ACTION_TYPES.GET_STUFF_PENDING
      });

      const data = await getStuff();

      dispatch({
        payload: data,
        type: ACTION_TYPES.GET_STUFF_COMPLETE
      });
    };
  };

  // good
  export const getStuff = () => {
    return async (dispatch) => {
      dispatch({
        type: ACTION_TYPES.GET_STUFF_PENDING
      });

      try {
        const data = await getStuff();

        dispatch({
          payload: data,
          type: ACTION_TYPES.GET_STUFF_SUCCESS
        });
      } catch (error) {
        dispatch({
          error: true,
          payload: error,
          type: ACTION_TYPES.GET_STUFF_FAIL
        })
      }
    };
  };
  ```

## Redux actions reducers

  - [5.1](#5.1) <a name='5.1'></a> **Use handleActions**: Use `handleActions` from the redux-actions package if using redux-actions for action creation.

  > Why? Creates a concise and consistent syntax.

  ```javascript
  // external dependencies
  import {
    handleActions
  } from 'redux-actions';

  // actions
  import {
    ACTION_TYPES
  } from 'actions/stuff';

  export const INITIAL_STATE = {
    error: null,
    isLoading: false,
    stuff: []
  };

  const handleFail = (state, {payload}) => {
    return {
      ...state,
      error: payload,
      isLoading: false
    };
  };

  const handlePending = (state) => {
    return {
      ...state,
      isLoading: true
    };
  };

  const handleSuccess = (state, {payload}) => {
    return {
      ...state,
      error: null,
      isLoading: false,
      stuff: payload
    };
  };

  export default handleActions({
    [ACTION_TYPES.GET_FAIL]: handleFail,
    [ACTION_TYPES.GET_PENDING]: handlePending,
    [ACTION_TYPES.GET_SUCCESS]: handleSuccess
  }, INITIAL_STATE);
  ```

## Manual reducers

  - [5.1](#5.1) <a name='5.1'></a> **Use dedicated handler functions**: Instead of updating the state in the reducer itself, create handler functions to update the state.

  > Why? Targeted, scoped functions are both easier to test and keep the reducer small and readable.

  ```javascript
  import {
    ACTION_TYPES
  } from 'actions/stuff';

  export const INITIAL_STATE = {
    error: null,
    isLoading: false,
    stuff: []
  };

  // bad
  export default (state = INITIAL_STATE, {payload, type}) => {
    switch (type) {
      case ACTION_TYPES.GET_STUFF_FAIL:
        return {
          ...state,
          error: payload,
          isLoading: false
        };

      case ACTION_TYPES.GET_STUFF_PENDING:
        return {
          ...state,
          isLoading: true
        };

      case ACTION_TYPES.GET_STUFF_SUCCESS:
        return {
          ...state,
          error: null,
          isLoading: false,
          stuff: payload
        };

      default:
        return state;
    }
  };

  // good
  const handleFail = (state, {payload}) => {
    return {
      ...state,
      error: payload,
      isLoading: false
    };
  };

  const handlePending = (state) => {
    return {
      ...state,
      isLoading: true
    };
  };

  const handleSuccess = (state, {payload}) => {
    return {
      ...state,
      error: null,
      isLoading: false,
      stuff: payload
    };
  };

  export default (state, action) => {
    switch (action.type) {
      case ACTION_TYPES.GET_STUFF_FAIL:
        return handleFail(state, action);

      case ACTION_TYPES.GET_STUFF_PENDING:
        return handlePending(state, action);

      case ACTION_TYPES.GET_STUFF_SUCCESS:
        return handleSuccess(state, action);

      default:
        return state;
    }
  };
  ```
