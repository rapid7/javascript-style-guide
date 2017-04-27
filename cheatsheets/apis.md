## Table of Contents

  1. [Structure](#structure)

## Structure

  - [1.1](#1.1) <a name='1.1'></a> **Async function vs. Promise**: Prefer the use of async functions for API calls.

  > Why? async functions have a more declarative and readable structure.

  ```javascript
  // promises
  const getStuff = () => {
    return axios.get('/api/1/stuff')
      .then((response) => {
        return response.data;
      });
  };

  // async
  const getStuff = async () => {
    const response = await axios.get('/api/1/stuff');

    return response.data;
  };
  ```

  - [1.2](#1.2) <a name='1.2'></a> **Avoid catching errors**: Prefer that the caller of the API handles errors instead of the API itself.

  > Why? You may want to handle errors from the API differently in different contexts, and handling it within the API limits reusability.

  ```javascript
  // bad
  const getStuff = async () => {
    try {
      const response = await axios.get('/api/1/stuff');

      return response.data;
    } catch (error) {
      console.error(error);

      return {};
    }
  };

  const loadStuff = async () => {
    const data = await getStuff();

    dispatch({
      payload: data,
      type: 'foo'
    });
  };

  // good
  const getStuff = async () => {
    const response = await axios.get('/api/1/stuff');

    return response.data;
  };

  const loadStuff = async () => {
    try {
      const data = await getStuff();

      dispatch({
        payload: data,
        type: 'foo'
      });
    } catch (error) {
      dispatch({
        error: true,
        payload: error,
        type: 'foo'
      });
    }
  };
  ```
