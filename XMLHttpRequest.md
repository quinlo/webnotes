# XMLHttpRequest (XHR)

Despite its name, `XMLHttpRequest` can be used to retrieve any type of data, not just XML, and it supports protocols other than HTTP (including file and ftp).

## The Constructor

+ `XMLHttpRequest()` The constructor initializes an XMLHttpRequest. It must be called before any other method calls.

## Properties

_This interface also inherits properties of `XMLHttpRequestEventTarget` and of `EventTarget`._

+ `XMLHttpRequest.onreadystatechange`: An `EventHandler` that is called whenever the `readyState` attribute changes.
+ `XMLHttpRequest.readyState` (Read only): 
Returns an unsigned short, the state of the request.
+ `XMLHttpRequest.response` (Read only):
Returns an `ArrayBuffer`, `Blob`, `Document`, JavaScript object, or a `DOMString`, depending on the value of `XMLHttpRequest.responseType`. that contains the response entity body.
+ `XMLHttpRequest.responseText` (Read only): Returns a `DOMString` that contains the response to the request as text, or `null` if the request was unsuccessful or has not yet been sent.
+ `XMLHttpRequest.responseType`: Is an enumerated value that defines the response type.
+ `XMLHttpRequest.responseURL` (Read only): Returns the serialized URL of the response or the empty string if the URL is null.
+ `XMLHttpRequest.responseXML` (Read only  Not available to workers): Returns a `Document` containing the response to the request, or `null` if the request was unsuccessful, has not yet been sent, or cannot be parsed as XML or HTML.
+ `XMLHttpRequest.status` (Read only): Returns an unsigned short with the status of the response of the request.
+ `XMLHttpRequest.statusText` (Read only): Returns a `DOMString` containing the response string returned by the HTTP server. Unlike `XMLHTTPRequest.status`, this includes the entire text of the response message ("200 OK", for example).

> The HTTP/2 specification (8.1.2.4 Response Pseudo-Header Fields), HTTP/2 does not define a way to carry the version or reason phrase that is included in an HTTP/1.1 status line.

+ `XMLHttpRequest.timeout`: Is an unsigned long representing the number of milliseconds a request can take before automatically being terminated.
+ `XMLHttpRequestEventTarget.ontimeout`: Is an `EventHandler` that is called whenever the request times out.
+ `XMLHttpRequest.upload` (Read only): Is an `XMLHttpRequestUpload`, representing the upload process.
+ `XMLHttpRequest.withCredentials`: Is a `Boolean` that indicates whether or not cross-site `Access-Control` requests should be made using credentials such as cookies or authorization headers.

### Event Handlers

`onreadystatechange` as a property of the `XMLHttpRequest` instance is supported in all browsers.

Since then, a number of additional event handlers have been implemented in various browsers (`onload`, `onerror`, `onprogress`, etc.). See Using `XMLHttpRequest`.

More recent browsers, including Firefox, also support listening to the `XMLHttpRequest` events via standard `addEventListener()` APIs in addition to setting `on*` properties to a handler function.

## Methods

+ `XMLHttpRequest.abort()`: Aborts the request if it has already been sent.
+ `XMLHttpRequest.getAllResponseHeaders()`: Returns all the response headers, separated by CRLF, as a string, or `null` if no response has been received.
+ `XMLHttpRequest.getResponseHeader()`: Returns the string containing the text of the specified header, or `null` if either the response has not yet been received or the header doesn't exist in the response.
+ `XMLHttpRequest.open()`: Initializes a request. This method is to be used from JavaScript code; to initialize a request from native code, use `openRequest()` instead.
+ `XMLHttpRequest.overrideMimeType()`: Overrides the MIME type returned by the server.
+ `XMLHttpRequest.send()`: Sends the request. If the request is asynchronous (which is the default), this method returns as soon as the request is sent.
+ `XMLHttpRequest.setRequestHeader()`: Sets the value of an HTTP request header. You must call `setRequestHeader()` after `open()`, but before `send()`.

## Using XMLHttpRequest

To send an HTTP request, create an `XMLHttpRequest` object, open a URL, and send the request. After the transaction completes, the object will contain useful information such as the response body and the HTTP status of the result.

```js
function reqListener () {
  console.log(this.responseText);
}

var oReq = new XMLHttpRequest();
oReq.addEventListener("load", reqListener);
oReq.open("GET", "http://www.example.org/example.txt");
oReq.send();
```

### Types of requests

Request made via `XMLHttpRequest` can fetch data __synchronously__ or __asynchronously__.  The type of request is dictated by the optional `async` argument that is set on the `XMLHttpRequest.open()` method (true by default).  It is rare that you would use synchronous requests.

### Handling Responses

When response types are non-text, some manipulation and analysis may occur:

#### Analyzing and manipulating the `responseXML` property

If using `XMLHttpRequest` to get the content of a remote XML document, the `responseXML` property will be a DOM object containing a parsed XML document.  There are 4 ways of analyzing this XML document.

1. Using XPath to address (or point to) parts of it.
2. Manually Parsing and serializing XML to strings or objects.
3. Using `XMLSerializer` to serialize DOM trees to strings or to files.
4. `RegExp` can be used if you always know the content of the XML document beforehand. You might want to remove line breaks, if you use `RegExp` to scan with regard to line breaks. However, this method is a "last resort" since if the XML code changes slightly, the method will likely fail.

#### Processing a responseText property containing an HTML document

If you use `XMLHttpRequest` to get the content of a remote HTML webpage, the   `responseText` property is a string containing the raw HTML.  There are three primary ways to analyze and parse this raw HTML string:

1. Use the `XMLHttpRequest.responseXML` property.
2. Inject the content into the body of a document fragment via `fragment.body.innerHTML` and traverse the DOM of the fragment.
3. `RegExp` can be used if you always know the content of the HTML `responseText` beforehand.

### Handling Binary Data

Although `XMLHttpRequest` is most commonly used to send and receive textual data, it can be used to send and receive binary content as well. To coerce the response of an `XMLHttpRequest` into sending binary data, utilize `overrideMimeType()` method on the `XMLHttpRequest` object.

```js
var oReq = new XMLHttpRequest();
oReq.open("GET", url);
// retrieve data unprocessed as a binary string
oReq.overrideMimeType("text/plain; charset=x-user-defined");
/* ... */
```

Since the `responseType` attribute now supports a number of additional content types, we can use that instead.

```js
var oReq = new XMLHttpRequest();

oReq.onload = function(e) {
  var arraybuffer = oReq.response; // not responseText
  /* ... */
}
oReq.open("GET", url);
oReq.responseType = "arraybuffer";
oReq.send();
```

### Monitoring Progress

`XMLHttpRequest` provides the ability to listen to various events that can occur while the request is being processed (progress notifications, error notifications, and so forth),

Support for DOM progress event monitoring of `XMLHttpRequest` transfers follows the specification for progress events: these events implement the `ProgressEvent` interface. The actual events you can monitor to determine the state of an ongoing transfer are:

+ `progress`: The amount of data that has been retrieved and has changed.
+ `load`: The transfer is complete; all data is now in the `response`
+ `error`:
+ `abort`: 

>Note: You need to add the event listeners before calling open() on the request. Otherwise the progress events will not fire.

```js
var oReq = new XMLHttpRequest();

oReq.addEventListener("progress", updateProgress);
oReq.addEventListener("load", transferComplete);
oReq.addEventListener("error", transferFailed);
oReq.addEventListener("abort", transferCanceled);

oReq.open();

// ...

// progress on transfers from the server to the client (downloads)
function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total * 100;
    // ...
  } else {
    // Unable to compute progress information since the total size is unknown
  }
}

function transferComplete(evt) {
  console.log("The transfer is complete.");
}

function transferFailed(evt) {
  console.log("An error occurred while transferring the file.");
}

function transferCanceled(evt) {
  console.log("The transfer has been canceled by the user.");
}
```