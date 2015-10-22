# xhr

A small xhr wrapper. Designed for use with [browserify](http://browserify.org/).

Browser support: IE8+ and everything else.

## Example

```js
var xhr = require("xhr")

xhr({
    body: someJSONString,
    uri: "/foo",
    headers: {
        "Content-Type": "application/json"
    }
}, function (err, resp, body) {
    // check resp.statusCode
})
```

## `var req = xhr(options, callback)`

```js
type XhrOptions = String | {
    useXDR: Boolean?,
    sync: Boolean?,
    uri: String,
    url: String,
    method: String?,
    timeout: Number?,
    headers: Object?,
    body: String?,
    json: Object?,
    username: String?,
    password: String?,
    withCredentials: Boolean?,
    responseType: String?
}
xhr := (XhrOptions, Callback<Response>) => Request
```
the returned object is either an [`XMLHttpRequest`][3] instance
    or an [`XDomainRequest`][4] instance (if on IE8/IE9 &&
    `options.useXDR` is set to `true`)

Your callback will be called once with the arguments
    ( [`Error`][5], `response` , `body` ) where the response is an object:
```js
{
    body: Object||String,
    statusCode: Number,
    method: String,
    headers: {},
    url: String,
    rawRequest: xhr
}
```
 - `body`: HTTP response body - [`xhr.response`][6], [`xhr.responseText`][7] or
    [`xhr.responseXML`][8] depending on the request type.
 - `rawRequest`: Original  [`XMLHttpRequest`][3] instance
    or [`XDomainRequest`][4] instance (if on IE8/IE9 &&
    `options.useXDR` is set to `true`)
 - `headers`: A collection of headers where keys are header names converted to lowercase


Your callback will be called with an [`Error`][5] if there is an error in the browser that prevents sending the request.
A HTTP 500 response is not going to cause an error to be returned.

## Shorthands

## `var req = xhr(url, callback)`
`xhr` may also be called with a simple string instead of a set of options. In this case, a GET request will be made to that url.

## `var req = xhr(url, options, callback)`
The above shorthand may also be called with the standard set of options.

## `var req = xhr.{post, put, patch, del, head, get}(options, callback)`
The `xhr` module has convience functions attached that will make requests with the given method.
Each function is named after its method, with the exception of `DELETE` which is called `xhr.del` for compatibility.

The method function shorthand may be combined with the above url shorthand for succinct and descriptive requests. For example,

```js
xhr.post('/post-to-me', function(err, resp) {
  console.log(resp.body)
})
```

or

```js
xhr.del('/delete-me', { headers: { my: 'auth' } }, function (err, resp) {
  console.log(resp.statusCode);
})
```

## Options

### `options.method`

Specify the method the [`XMLHttpRequest`][3] should be opened
    with. Passed to [`xhr.open`][2]. Defaults to "GET"

### `options.useXDR`

Specify whether this is a cross origin (CORS) request for IE<10.
    Switches IE to use [`XDomainRequest`][4] instead of `XMLHttpRequest`.
    Ignored in other browsers.

Note that headers cannot be set on an XDomainRequest instance.

### `options.sync`

Specify whether this is a synchrounous request. Note that when
    this is true the callback will be called synchronously. In
    most cases this option should not be used. Only use if you
    know what you are doing!

### `options.body`

Pass in body to be send across the [`XMLHttpRequest`][3].
    Generally should be a string. But anything that's valid as
    a parameter to [`xhr.send`][1] should work

### `options.uri` or `options.url`

The uri to send a request to. Passed to [`xhr.open`][2]. `options.url` and `options.uri` are aliases for each other.

### `options.headers`

An object of headers that should be set on the request. The
    key, value pair is passed to [`xhr.setRequestHeader`][9]

### `options.timeout`

Number of miliseconds to wait for response. Defaults to 0 (no timeout). Ignored when `options.sync` is true.

### `options.json`

A valid JSON serializable value to be send to the server. If this
    is set then we serialize the value and use that as the body.
    We also set the Content-Type to `"application/json"`.

Additionally the response body is parsed as JSON

### `options.withCredentials`

Specify whether user credentials are to be included in a cross-origin
    request. Sets [`xhr.withCredentials`][10]. Defaults to false.

A wildcard `*` cannot be used in the `Access-Control-Allow-Origin` header when `withCredentials` is true.
    The header needs to specify your origin explicitly or browser will abort the request.

### `options.responseType`

Determines the data type of the `response`. Sets [`xhr.responseType`][11]. For example, a `responseType` of `document` will return a parsed `Document` object as the `response.body` for an XML resource.

### `options.xhr`

Pass an `XMLHttpRequest` object (or something that acts like one) to use instead of constructing a new one using the `XMLHttpRequest` or `XDomainRequest` constructors. Useful for testing.

## Mocking Requests
You can override the constructor used to create new requests for testing. When you're making a new request:

```js
xhr({ xhr: new MockXMLHttpRequest() })
```

or you can override the constructors used to create requests at the module level:

```js
xhr.XMLHttpRequest = MockXMLHttpRequest
xhr.XDomainRequest = MockXDomainRequest
```

## MIT Licenced

  [1]: http://xhr.spec.whatwg.org/#the-send()-method
  [2]: http://xhr.spec.whatwg.org/#the-open()-method
  [3]: http://xhr.spec.whatwg.org/#interface-xmlhttprequest
  [4]: http://msdn.microsoft.com/en-us/library/ie/cc288060(v=vs.85).aspx
  [5]: http://es5.github.com/#x15.11
  [6]: http://xhr.spec.whatwg.org/#the-response-attribute
  [7]: http://xhr.spec.whatwg.org/#the-responsetext-attribute
  [8]: http://xhr.spec.whatwg.org/#the-responsexml-attribute
  [9]: http://xhr.spec.whatwg.org/#the-setrequestheader()-method
  [10]: http://xhr.spec.whatwg.org/#the-withcredentials-attribute
  [11]: https://xhr.spec.whatwg.org/#the-responsetype-attribute
