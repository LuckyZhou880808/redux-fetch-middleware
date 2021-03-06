# redux-fetch-middleware

[![Join the chat at https://gitter.im/zslucky/redux-fetch-middleware](https://badges.gitter.im/zslucky/redux-fetch-middleware.svg)](https://gitter.im/zslucky/redux-fetch-middleware?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/zslucky/redux-fetch-middleware.svg?branch=master)](https://travis-ci.org/zslucky/redux-fetch-middleware)
[![Known Vulnerabilities](https://snyk.io/test/github/zslucky/redux-fetch-middleware/badge.svg?targetFile=package.json)](https://snyk.io/test/github/zslucky/redux-fetch-middleware?targetFile=package.json)

A middleware for redux that help to fetch data from rest API and simplify the request flow.
Many times we only need to do some simple request, but we need to track the request status,
This middleware will automaticly dispatch 3 status.

Changes plesae refer to [CHANGELOG.md](./CHANGELOG.md)

**In V4 version, we removed `isomorphic-fetch` dependency, as this is only a polyfill, you can add it everywhere by yourself in your own project if you need it.**

## Installation

```sh
npm i redux-fetch-middleware --save
```
Or use `yarn`:
```sh
yarn add redux-fetch-middleware
```

## Usage

```javascript
import restMiddlewareCreator from 'redux-fetch-middleware';
import { applyMiddleware } from 'redux';

const globalRestOptions = {
    suffix: ['REQUEST', 'SUCCESS', 'FAILURE'],
    // if `debug` is true, then in reducer `action.meta.$requestOptions`
    debug: true,
    // Set global value by `responseType`. Available values: json, text, formData, blob, arrayBuffer (fetch methods). Default: json
    responseType: 'text',
    // Example config
    fetchOptions: {
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
        }
    }
};

const restMiddleware = restMiddlewareCreator(globalRestOptions);

const middleware = [restMiddleware];
applyMiddleware(...middleware);

//...
```

## Configuration
Fetch options and browser support please refer to [whatwg-fetch](https://www.npmjs.com/package/whatwg-fetch)

### For global settings

#### Every options have default value, please kindly reduce configuration. :shipit:

```javascript
{
    // Suffix will auto append to every action type, then we can dispatch
    // different situation.
    // @Fisrt parameter - 'Request' means when request start.
    // @Second parameter - 'SUCCESS' means when we get response successfully.
    // @Third parameter - 'FAILURE' means when something error.
    // name can be defined by self.
    // Default value is bellow.
    suffix: ['REQUEST', 'SUCCESS', 'FAILURE'],

    // The global fetch settings for our middleware
    fetchOptions: {

        // For detail please relay to whatwg-fetch
        // This is the default header settings.
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
        }

    },

    /* TBD ...
     *
     * Here is globle config for each action.
     * Other amazing functions
     *
     */ TBD ...
}

```
### Action usage

```javascript
{
    // Type name
    type: YOUR_ACTION_TYPE_NAME,
    // Your props transition to reducers
    meta: {
        a: 10,
        b: 20
    },
    // @Param: $payload is the detail ajax request description
    $payload: {
        // Request url
        url: '/api/somewhere',
        // React on response event. If this function return === false, then to SUCCESS reducer data = null
        onResponse: (response, meta, type) {
            if (response.status != 200) {
                return false;
            }
        }
        // React pre fetch edit options. Include merged all options. Return the last options.
        preFetchOptions: (options) {
            // For example - remove Content-Type, in order to browser auto detect and auto write Content-Type value (Required to send file).
            delete options.headers['Content-Type'];
            return options;
        }
        // Method type to parse response. Available values: json, text, formData, blob, arrayBuffer (fetch methods). Default: json
        responseType: 'text',
        // The specific options for current request.
        options: {

            // Same as whatwg-fetch

        }

        /* TBD ...
         *
         * Here is config for single action.
         * Other amazing functions
         *
         */ TBD ...
        }
}
```

### Reducer usage
```javascript
function yourReducer(state = initialState, action) {
    switch (action.type) {
        case `${YOUR_ACTION_TYPE_NAME}_REQUEST`:
            // Do something when request start ...
            // @response meta is action.meta
            // @response $uid is action.meta.$uid
            // @response $requestOptions is action.meta.$requestOptions (if in config set `debug` is true)

        case `${YOUR_ACTION_TYPE_NAME}_SUCCESS`:
            // Do something ...
            // @response data is action.data
            // @response meta is action.meta
            // @response $uid is action.meta.$uid
            // @response $response is action.meta.$response
            // @response $requestOptions is action.meta.$requestOptions (if in config set `debug` is true)

        case `${YOUR_ACTION_TYPE_NAME}_FAILURE`:
            // Do something other ...
            // @response data is action.err
            // @response meta is action.meta
            // @response $uid is action.meta.$uid
            // @response $response is action.meta.$response
            // @response $requestOptions is action.meta.$requestOptions (if in config set `debug` is true)

        default:
            return state;
    }
}
```
## Migrate from v3 to v4
1. Remove `isomophic-fetch` dependency.

## Migrate from v2.* to v.3
1. Replace `$props` to `meta` in your action and reducers .
2. Replace `$uid` to `meta.$uid` in your reducers.
3. Profit!

## TO DO List
1. [ ] Add custom Exception config and response status trace.
2. [ ] Improve unit test.
