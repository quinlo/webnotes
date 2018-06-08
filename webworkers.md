# Using WebWorkers

When executing scripts in an HTML page, the page becomes unresponsive until the script is finished.

A web worker is a JavaScript that runs in the background, independently of other scripts, without affecting the performance of the page. You can continue to do whatever you want: clicking, selecting things, etc., while the web worker runs in the background.

## The file that creates and talks to the WebWorker

Before creating a web worker, check whether the user's browser supports it:

```js
if (window.Worker) {

  ...

}
```

Creating a new worker is simple.  All you need to do is call the `Worker()` constructor, specifying the URI of a script to execute in the worker thread.

```js
var loader = new Worker('utils/loader.js');
```

> If you specify a relative URL it is resolved relative to the URL of the document that contains the script that called the `Worker()` constructor.  If you specify an absolute URL, it must have the same origin (protocol, host, and port) as that containing document.

Once you have a Worker object you can send data to it with `postMessage()`.  The value you pass to `postMessage()` will be cloned and the resulting copy will be delivered to the worker via a message event:

```js
loader.postMessage("file.txt");
```

__Retreive__ messages from a worker by listening for message events on the Worker object.

```js
loader.onmessage = function(e){
    var message = e.data;           //get message from event
    console.log("URL contents: " + message);        // Do Something with it.
}
```

If a worker throws an exception and does not catch or handle it itself, that exception propagates as an event that you can listen for:

```js
loader.onerror = function(e){
    //Log the error message, including worker filename and line number
    console.log("Error at " + e.filename + ":" + e.lineno + ": " + e.message);
}
```

The last method that a worker object has is `terminate()` which forces a worker thread to stop running.

## The WebWorker itself

`postMessage()` and `onmessage` work the same way in the webworker but note that since the WorkerGlobalScope is the global object for a worker, they look like a global function and a global variable to worker code.

The `close()` function allows a worker to terminate itself and is similar in effect to the `terminate()` method of a Worker object.  In the code talking to the webworker there is no way of knowing that the worker 'closed' itself so it is a good idea to send a message before `close()`.

The most interesting global function defined by WorkerGlobalScope is `importScripts()`: workers use this function to load any library code they require.  For example:

```js
//Before we start working, load the classes and utilities we'll need
importScripts("collection/Set.js", "collections/Map.js", "utils/base64.js");
```

+ Relative URLS are relative to the worker.
+ If a script doesn't load correctly none of the subsequent scripts are loaded or executed.
+ A script loaded by `importScripts()` can itself import scripts.
+ `importScripts()` is a synchronous function and will not return until all of the scripts have loaded and executed.

### Properties and Constructors of the WorkerGlobalScope

Since WorkerGlobalScope is the global object ofr workers, it has all of the properties of the core JavaScript global object but has some additional properties:

+ `self`: reference the to global object itself.
+ `setTimeout()`, `clearTimeout()`, `setInterval()`, `clearInterval()`
+ `location`: describes the URL passed ot the `Worker()` constructor.  This property refers to a Location object, and so has properties: `href`, `protocol`, `host`, `hostname`, `port`, `pathname`, `search`, and `hash`
+ `navigator`: refers to an object with properties like those of navigator object of a window.  Has properties: `appName`, `appVersion`, `platform`, `userAgent`, and `onLine`.
+ The usual `addEventListener()` and `removeEventListener()`
+ `onerror`: similar to the `onerror` property discussed earlier that has properties: `filename`, `lineno`, and `message`

The WorkerGlobalScope object includes important client-side JavaScript constructor objects including:

+ `XMLHttpRequest()` : fire off HTTP requests
+ `Worker()`: Allows worker to spawn additional workers