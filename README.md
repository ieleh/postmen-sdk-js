## Introduction
Node.js SDK for [Postmen API](https://docs.postmen.com/).
For problems and suggestions please open [GitHub issue](https://github.com/postmen/postmen-sdk-js/issues)

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Installation](#installation)
    - [Requirements](#requirements)
    - [Manual installation](#manual-installation)
    - [Using Composer](#using-composer)
- [Quick Start](#quick-start)
- [class Postmen](#class-postmen)
    - [Postmen(api_key, region, config = array())](#postmenapi_key-region-config--array)
    - [create(resource, payload, config = array())](#createresource-payload-config--array)
    - [get(resource, id = NULL, query = array(), config = array())](#getresource-id--null-query--array-config--array)
    - [callGET(path, query = array(), options = array())](#callgetpath-query--array-options--array)
    - [callPOST(path, body = array(), options = array())](#callpostpath-body--array-options--array)
    - [callPUT(path, body = array(), options = array())](#callputpath-body--array-options--array)
    - [callDELETE(path, body = array(), options = array())](#calldeletepath-body--array-options--array)
- [Rate Limiter](#rate-limiter)
- [Error Handling](#error-handling)
    - [class PostmenException](#class-postmenexception)
    - [Automatic retry on retryable error](#automatic-retry-on-retryable-error)
- [Examples](#examples)
    - [Full list](#full-list)
    - [How to run](#how-to-run)
    - [Navigation table](#navigation-table)
- [Testing](#testing)
- [License](#license)
- [Contributors](#contributors)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Installation

#### Requirements

Node.js `>= 4.2` is required. 

#### Manual installation

```
npm install aftership
```

## Quick Start

In order to get API key and choose a region refer to the [documentation](https://docs.postmen.com/overview.html).

```javascript
'use strict';

const Postmen = require('./../index');
// TODO key of the Postmen instance
let api_key = 'api-key',
// TODO region of the Postmen instance
let region = 'sandbox';
	
// get all  labels
postmen.get('/labels', function (err, result) {
	if (err) {
		console.log(err);
	} else {
		console.log(result);
	}
});

// get a particular  labels
postmen.get('/rates/put-your-label-id-here', function (err, result) {
	if (err) {
		console.log(err);
	} else {
		console.log(result);
	}
});

```

## class Postmen

#### Postmen(api_key, region, config)

Initiate Postmen SDK object.
In order to get API key and choose a region refer to the [documentation](https://docs.postmen.com/overview.html).

| Argument                       | Required                               | Type    | Default   | Description                                       |
|--------------------------------|----------------------------------------|---------|-----------|---------------------------------------------------|
| `api_key`                     | YES                                    | string  | N/A     | API key                                           |
| `region`                      | NO if `config['endpoint']` is set     | string  | N/A     | API region (`sandbox`, `production`)              |
| `config`                      | NO                                     | object   | null | Options                                           |
| `config['endpoint']`          | —                                      | string  | null     | Custom URL API endpoint                           |
| `config['retry']`             | —                                      | boolean | `true`    | Automatic retry on retryable errors               |
| `config['rate']`              | —                                      | boolean | `true`    | Wait before API call if rate limit exceeded or retry on 429 error |
| `config['raw']`               | —                                      | boolean | `false`   | To return API response as a raw string            |
| `config['proxy']`             | —                                      | string   |  | Proxy credentials                                 |

#### create(path, config, callback)
Creates postmen api object

| Argument                       | Required                               | Type    | Default   | Description                                       |
|--------------------------------|----------------------------------------|---------|-----------|---------------------------------------------------|
| `path`                     | YES                                    | string  | N/A     | start with `/`, see available path [here](https://docs.postmen.com/index.html) key                                           |
| `config`                      | YES                                     | object   | `null` | object of request config |
| `config['body']`          |   YES                                    | string  | `null`     |      `POST` body                   |
| `config['query']`             | NO                                     | object | `null`    | `query` object              |
| `config['retry']`              | NO                                     | boolean | `true`    | override `default retry` if set, see [Retry policy](#retry-policy) |
| `config['raw']`               | —                                      | boolean | `false`   | if `true`, return result as `string`, else return as `object`           |
| `callback`              | YES                                    | function | N/A   | the callback to handle error and result, the result is the response body of the request|

- `path` - **Required** *string*, start with `/`, see available path [here](https://docs.postmen.com/index.html)
- `config` - **Required** object, object of request config
	- `body` - **Required**  *object*, `POST` body
	- `query` - *object*, `query` object
	- `retry` - *boolean*, retry if failed, override `default retry` if set, see [Retry policy](#retry-policy)
	- `raw` - *boolean*, if `true`, return result as `string`, else return as `object`, default is `false`
- `callback` - the callback to handle error and result, the result is the response body of the request

**API Docs:**
- [POST /rates](https://docs.postmen.com/#rates-calculate-rates)
- [POST /labels](https://docs.postmen.com/#labels-create-a-label)
- [POST /manifests](https://docs.postmen.com/#manifests-create-a-manifest)
- [POST /cancel-labels](https://docs.postmen.com/#cancel-labels-cancel-a-label)

**Examples:**
- [rates_create.js](https://github.com/postmen/postmen-sdk-Node.js/blob/master/examples/rates_create.js)
- [labels_create.js](https://github.com/postmen/postmen-sdk-Node.js/blob/master/examples/labels_create.js)
- [manifests_create.js](https://github.com/postmen/postmen-sdk-Node.js/blob/master/examples/manifests_create.js)
- [cancel_labels_create.js](https://github.com/postmen/postmen-sdk-Node.js/blob/master/examples/cancel_labels_create.js)

#### get(path, config,callback)
Gets Postmen API  objects (list or a single objects).

| Argument                       | Required                               | Type    | Default   | Description                                       |
|--------------------------------|----------------------------------------|---------|-----------|---------------------------------------------------|
| `path`                     | YES                                    | string  | N/A     | start with `/`, see available path [here](https://docs.postmen.com/index.html) key                                           |
| `config`                      | NO                                     | object   | `null` | object of request config |
| `config['body']`          |   NO                                    | string  | `null`     |      `POST` body                   |
| `config['query']`             | NO                                     | object | `null`    | `query` object              |
| `config['retry']`              | NO                                     | boolean | `true`    | override `default retry` if set, see [Retry policy](#retry-policy) |
| `config['raw']`               | —                                      | boolean | `false`   | if `true`, return result as `string`, else return as `object`           |
| `callback`              | YES                                    | function | N/A   | the callback to handle error and result, the result is the response body of the request|

```javascript
postmen.get( '/path/label-id', callback);
// is equivalent to
postmen.call('GET', '/path/label-id', {}, callback);

postmen.get( '/path', config, callback);
// is equivalent to
postmen.call('GET', '/path', config, callback);
```
**API Docs:**
- [GET /rates](https://docs.postmen.com/#rates-list-all-rates)
- [GET /rates/:id](https://docs.postmen.com/#rates-retrieve-rates)
- [GET /labels](https://docs.postmen.com/#labels-list-all-labels)
- [GET /labels/:id](https://docs.postmen.com/#labels-retrieve-a-label)
- [GET /manifests](https://docs.postmen.com/#manifests-list-all-manifests)
- [GET /manifests/:id](https://docs.postmen.com/#manifests-retrieve-a-manifest)
- [GET /cancel-labels](https://docs.postmen.com/#cancel-labels-list-all-cancel-labels)
- [GET /cancel-labels/:id](https://docs.postmen.com/#cancel-labels-retrieve-a-cancel-label)

**Examples:**
- [rates_retrieve.php](https://github.com/postmen/postmen-sdk-js/master/examples/rates_retrieve.php)
- [labels_retrieve.php](https://github.com/postmen/postmen-sdk-js/master/examples/labels_retrieve.php)
- [manifests_retrieve.php](https://github.com/postmen/postmen-sdk-js/master/examples/manifests_retrieve.php)
- [cancel_labels_retrieve.php](https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_retrieve.php)

**Rate Limiter:**

To understand AfterShip rate limit policy, please see `limit` session in https://docs.postmen.com/ratelimit.html

You can get the recent rate limit by `postmen.rate_limit`. Initially all value is `{}`.
```javascript
let postmen = Postmen('YOUR_API_KEY', 'region');
console.log(postmen.rate_limit);

// console output
// {}
```
After making an API call, it will be set.
```javascript
postmen.get('/labels', function (err, result) {
	console.log(postmen.rate_limit);
});

// console output
// { 'YOUR_API-KEY' : { limit: 600, remaining: 599, reset: 1453281417 } }
```

When the API response with `429 Too Many request error`
- if `rate` is `true`, it wont throw, will delay the job, retry when the rate limit is reset.
- if `rate` is `false`, it will return `429 Too Many request error` to the callback

## Proxy Method (GET, POST, PUT, DELETE)

There are also interface `GET`, `POST`, `PUT`, `DELETE` which are proxy to `Aftership.call(...)`

```javascript
postmen.call('GET', '/path', config, callback);
// is equivalent to
postmen.GET('/path', config, callback);

// So as `POST`, `PUT` and `DELETE`
```
## Examples

#### Full list
All examples avalible listed in the table below.

| File                                                                                                                     | Description                        |
|--------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| [rates_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/rates_create.js)                     | `rates` object creation            |
| [rates_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/rates_retrieve.js)                 | `rates` object(s) retrieve         |
| [labels_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/labels_create.js)                   | `labels` object creation           |
| [labels_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/labels_retrieve.js)               | `labels` object(s) retrieve        |
| [manifests_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/manifests_create.js)             | `manifests` object creation        |
| [manifests_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/manifests_retrieve.js)         | `manifests` object(s) retrieve     |
| [cancel_labels_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_create.js)     | `cancel-labels` object creation    |
| [cancel_labels_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_retrieve.js) | `cancel-labels` object(s) retrieve |
| [proxy.js](https://github.com/postmen/postmen-sdk-js/master/examples/proxy.js)                                   | Proxy usage                        |
| [response.js](https://github.com/postmen/postmen-sdk-js/master/examples/response.js)                             | Avalible output types              |

#### How to run

Download the source code, go to `examples` directory.

Put your API key and region to [credentials.js](https://github.com/postmen/postmen-sdk-js/master/examples/credentials.js)

Check the file you want to run before run. Some require you to set additional variables.

#### Automatic retry on retryable error

If API error is retryable, SDK will wait for delay and retry. Delay starts from 1 second. After each try, delay time is doubled. Maximum number of attempts is 5.

To disable this option set `conifg['retry'] = FALSE;`

## Examples

#### Full list
All examples avalible listed in the table below.

| File                                                                                                                     | Description                        |
|--------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| [rates_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/rates_create.js)                     | `rates` object creation            |
| [rates_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/rates_retrieve.js)                 | `rates` object(s) retrieve         |
| [labels_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/labels_create.js)                   | `labels` object creation           |
| [labels_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/labels_retrieve.js)               | `labels` object(s) retrieve        |
| [manifests_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/manifests_create.js)             | `manifests` object creation        |
| [manifests_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/manifests_retrieve.js)         | `manifests` object(s) retrieve     |
| [cancel_labels_create.js](https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_create.js)     | `cancel-labels` object creation    |
| [cancel_labels_retrieve.js](https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_retrieve.js) | `cancel-labels` object(s) retrieve |
| [proxy.js](https://github.com/postmen/postmen-sdk-js/master/examples/proxy.js)                                   | Proxy usage                        |
| [error.js](https://github.com/postmen/postmen-sdk-js/master/examples/error.js)                                   | Avalible ways to catch/get errors  |
| [response.js](https://github.com/postmen/postmen-sdk-js/master/examples/response.js)                             | Avalible output types              |

#### How to run

Download the source code, go to `examples` directory.

Put your API key and region to [credentials.js](https://github.com/postmen/postmen-sdk-js/master/examples/credentials.js)

Check the file you want to run before run. Some require you to set additional variables.

#### Navigation table

For each API method SDK provides Node.js wrapper. Use the table below to find SDK method and example that match your need.

<table>
  <tr>
    <th>Model \ Action</th>
    <th>create</th>
    <th>get all</th>
    <th>get by id</th>
  </tr>
  <tr>
    <th>rates</th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/rates_create.js#L78">
      <code>.create('/rates', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/rates_retrieve.js#L9">
      <code>.get('/rates', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/rates_retrieve.js#L18">
      <code>.get('rates/rate-id-here', config, callback)</code>
    </a></sub></th>
  </tr>
  <tr>
    <th>labels</th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/labels_create.js#L98">
      <code>.create('/labels', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/labels_retrieve.js#L9">
      <code>.get('/labels', config, callback))</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/labels_retrieve.js#L18">
      <code>.get('/labels/label-id-here', config, callback))</code>
    </a></sub></th>
  </tr>
  <tr>
    <th>manifest</th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/manifests_create.js">
      <code>.create('/manifest', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/manifests_retrieve.js#L9">
      <code>.get('/manifest', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/manifests_retrieve.js#L19">
      <code>.get('/manifest/manifest-id-here', id, NULL, opt)</code>
    </a></sub></th>
  </tr>
  <tr>
    <th>cancel-labels</th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_create.js">
      <code>.create('/cancel-labels', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_retrieve.js#L9">
      <code>.get('/cancel-labels', config, callback)</code>
    </a></sub></th>
    <th><sub><a href="https://github.com/postmen/postmen-sdk-js/master/examples/cancel_labels_retrieve.js#L18">
      <code>.get('/cancel-labels/cancel-labels-id-here', config, calback)</code>
    </a></sub></th>
  </tr>
</table>

## Testing
```
mocha --recursive
```
## License
Released under the MIT license. See the LICENSE file for details.

## Contributors
- Adam Liu - [view contributions](https://github.com/postmen/postmen-sdk-js/commits?author=adamliuxy)
- Chiu Lam - [view contributions](https://github.com/AfterShip/aftership-sdk-nodejs/commits?author=chiulam)
