# XMLHttpRequest (XHR)

Despite its name, `XMLHttpRequest` can be used to retrieve any type of data, not just XML, and it supports protocols other than HTTP (including file and ftp).

### The Constructor

+ `XMLHttpRequest()` The constructor initializes an XMLHttpRequest. It must be called before any other method calls.

### The Properties

_This interface also inherits properties of `XMLHttpRequestEventTarget` and of `EventTarget`._

+ `XMLHttpRequest.onreadystatechange`: An EventHandler that is called whenever the readyState attribute changes.
+ `XMLHttpRequest.readyState` (Read only): 
Returns an unsigned short, the state of the request.
+ `XMLHttpRequest.response` (Read only):
Returns an ArrayBuffer, Blob, Document, JavaScript object, or a DOMString, depending on the value of XMLHttpRequest.responseType. that contains the response entity body.
XMLHttpRequest.responseText Read only
Returns a DOMString that contains the response to the request as text, or null if the request was unsuccessful or has not yet been sent.
XMLHttpRequest.responseType
Is an enumerated value that defines the response type.
XMLHttpRequest.responseURL Read only
Returns the serialized URL of the response or the empty string if the URL is null.
XMLHttpRequest.responseXML Read only  Not available to workers
Returns a Document containing the response to the request, or null if the request was unsuccessful, has not yet been sent, or cannot be parsed as XML or HTML.
XMLHttpRequest.status Read only
Returns an unsigned short with the status of the response of the request.
XMLHttpRequest.statusText Read only
Returns a DOMString containing the response string returned by the HTTP server. Unlike XMLHTTPRequest.status, this includes the entire text of the response message ("200 OK", for example).
Note: The HTTP/2 specification (8.1.2.4 Response Pseudo-Header Fields), HTTP/2 does not define a way to carry the version or reason phrase that is included in an HTTP/1.1 status line.

XMLHttpRequest.timeout
Is an unsigned long representing the number of milliseconds a request can take before automatically being terminated.
XMLHttpRequestEventTarget.ontimeout
Is an EventHandler that is called whenever the request times out.
XMLHttpRequest.upload Read only
Is an XMLHttpRequestUpload, representing the upload process.
XMLHttpRequest.withCredentials
Is a Boolean that indicates whether or not cross-site Access-Control requests should be made using credentials such as cookies or authorization headers.