# I love me some Ajax

Ajax is the popular name for web application programming techniques that use HTTP scripting to load data as needed, without causing page refreshes.

Asynchronous Javascript and XML can be accomplished with [XHRs](xmlhttprequest) but jquery has a handy dandy helper which is what is dicussed here. With the jQuery AJAX methods, you can request text, HTML, XML, or JSON from a remote server using both HTTP Get and HTTP Post - And you can load the external data directly into the selected HTML elements of your web page

## jQuery AJAX methods

### the `load()` method

The jQuery `load()` method loads data from a server and puts the returned data into the selected element.

__$(selector).load(URL,data,callback);__

+ the required URL parameter specifies the URL you wish to load.
+ the optional data parameter specifies a set of querystring key/value pairs to send along with the request
+ the optional callback parameter is the name of a function to be executed after the `load()` method is completed.

```html
<!-- demo_test.txt -->
<h2>jQuery and AJAX is FUN!!!</h2>
<p id="p1">This is some text in a paragraph.</p>
```

```js
//js file
$("#div1").load("demo_test.txt");
```

It is also possible to add a jQuery selector to the URL parameter to load JUST the contents of the element selected:

```js
$("#div1").load("demo_test.txt #p1");
```

The callback function can have different parameters:

+ `responseTxt` - contains the resulting content if the call succeeds
+ `statusTxt` - contains the status of the call
+ `xhr` - contains the XMLHttpRequest object

```js
$("button").click(function(){
    $("#div1").load("demo_test.txt", function(responseTxt, statusTxt, xhr){
        if(statusTxt == "success")
            alert("External content loaded successfully!");
        if(statusTxt == "error")
            alert("Error: " + xhr.status + ": " + xhr.statusText);
    });
});
```

### AJAX get() and post() methods

The jQuery get() and post() methods are used to request data from the server with an HTTP GET or POST request.

The $.get() method requests data from the server with an HTTP GET request.

__$.get(URL,callback);__

+ The required URL parameter specifies the URL you wish to request.
+ The optional callback parameter is the name of a function to be executed if the request succeeds.

```js
$("button").click(function(){
    $.get("demo_test.asp", function(data, status){
        alert("Data: " + data + "\nStatus: " + status);
    });
});
```

The first callback parameter holds the content of the page requested, and the second callback parameter holds the status of the request.

The $.post() method requests data from the server using an HTTP POST request.

__$.post(URL,data,callback);__

+ The required URL parameter specifies the URL you wish to request
+ The optional data parameter specifies some data to send alont with the request
+ The optional callback parameter is the name of a function to be executed if the request succeeds

```js
$("button").click(function(){
    $.post("demo_test_post.asp",
    {
        name: "Donald Duck",
        city: "Duckburg"
    },
    function(data, status){
        alert("Data: " + data + "\nStatus: " + status);
    });
});
```

### The jQuery noConflict() method

What if other JavaScript frameworks also use the $ sign as a shortcut?

The noConflict() method releases the hold on the $ shortcut identifier, so that other scripts can use it. You can of course still use jQuery, simply by writing the full name instead of the shortcut:

```js
$.noConflict();
jQuery(document).ready(function(){
    jQuery("button").click(function(){
        jQuery("p").text("jQuery is still working!");
    });
});
```

The noConflict() method returns a reference to jQuery, that you can save in a variable, for later use. Here is an example:

```js
var jq = $.noConflict();
jq(document).ready(function(){
    jq("button").click(function(){
        jq("p").text("jQuery is still working!");
    });
});
```

If you have a block of jQuery code which uses the $ shortcut and you do not want to change it all, you can pass the $ sign in as a parameter to the ready method. This allows you to access jQuery using $, inside this function - outside of it, you will have to use "jQuery":

```js
$.noConflict();
jQuery(document).ready(function($){
    $("button").click(function(){
        $("p").text("jQuery is still working!");
    });
});
```

## The jQuery ajax() method

The ajax() method is used to perform an AJAX (asynchronous HTTP) request.  This method is used to have more granular control over your AJAX request.

__$.ajax({name:value, name:value, ... })__

The parameters specifies one or more name/value pairs for the AJAX request.  The possible name/valuse are as follows:

| Name | Value/Description |
--- | --- | ---
| async	| A Boolean value indicating whether the request should be handled asynchronous or not. Default is true |
| beforeSend(xhr)	| A function to run before the request is sent |
| cache	| A Boolean value indicating whether the browser should cache the requested pages. Default is true |
| complete(xhr,status)	| A function to run when the request is finished (after success and error functions) |
| contentType	| The content type used when sending data to the server. Default is: "application/x-www-form-urlencoded" |
| context	| Specifies the "this" value for all AJAX related callback functions |
| data	| Specifies data to be sent to the server |
| dataFilter(data,type)	| A function used to handle the raw response data of the XMLHttpRequest |
| dataType	| The data type expected of the server response. |
| error(xhr,status,error)	| A function to run if the request fails. |
| global	| A Boolean value specifying whether or not to trigger global AJAX event handles for the request. Default is true |
| ifModified	| A Boolean value specifying whether a request is only successful if the response has changed since the last request. Default is: false. |
| jsonp	| A string overriding the callback function in a jsonp request |
| jsonpCallback	| Specifies a name for the callback function in a jsonp request |
| password	| Specifies a password to be used in an HTTP access authentication request. |
| processData	| A Boolean value specifying whether or not data sent with the request should be transformed into a query string. Default is true |
| scriptCharset	| Specifies the charset for the request |
| success(result,status,xhr)	| A function to be run when the request succeeds |
| timeout	| The local timeout (in milliseconds) for the request |
| traditional	| A Boolean value specifying whether or not to use the traditional style of param serialization |
| type	| Specifies the type of request. (GET or POST) |
| url	| Specifies the URL to send the request to. Default is the current page |
| username	| Specifies a username to be used in an HTTP access authentication request |
| xhr	| A function used for creating the XMLHttpRequest object |

#### Examples

Make an AJAX request with a specified data type:

```html
<html>
<head>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script>
$(document).ready(function(){
    $("button").click(function(){
        $.ajax({url: "demo_ajax_script.js", dataType: "script"});
    });
});
</script>
</head>
<body>

<button>Use Ajax to get and then run a JavaScript</button>

</body>
</html>
```
Make an AJAX request with an error:

```html
<!DOCTYPE html>
<html>
<head>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script>
$(document).ready(function(){
    $("button").click(function(){
        $.ajax({url: "wrongfile.txt", error: function(xhr){
            alert("An error occured: " + xhr.status + " " + xhr.statusText);
        }});
    });
});
</script>
</head>
<body>

<p>Artists</p>
<div></div>

<button>Get CD info</button>

</body>
</html>
```