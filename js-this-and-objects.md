# Javascript - `this` and Object Prototypes

## Understanding `this`

Determining the `this` binding for an executing function requires finding the direct call-site of that function. Once examined, four rules can be applied to the call-site, in this order of precedence:

Called with `new`? Use the newly constructed object.

Called with `call` or `apply` (or `bind`)? Use the specified object.

Called with a context object owning the call? Use that context object.

Default: `undefined` in `strict mode`, global object otherwise.

Be careful of accidental/unintentional invoking of the default binding rule. In cases where you want to "safely" ignore a `this` binding, a "DMZ" object like `ø = Object.create(null)` is a good placeholder value that protects the `global` object from unintended side-effects.

Instead of the four standard binding rules, ES6 arrow-functions use lexical scoping for this binding, which means they adopt the `this` binding (whatever it is) from its enclosing function call. They are essentially a syntactic replacement of `self = this` in pre-ES6 coding.

## Objects

### Two Forms

literal syntax:

```js
var myObj = {
	key: value
	// ...
};
```

constructed form:

```js
var myObj = new Object();
myObj.key = value;
```

### Javascript types

There are six primitive types in javascript:
+ `string`
+ `number`
+ `boolean`
+ `null`
+ `undefined`
+ `object`

Additionally there are sub-types that are just built-in functions that can be used as a constructor (used with `new` operator):

+ `String`
+ `Number`
+ `Boolean`
+ `Object`
+ `Function`
+ `Array`
+ `Date`
+ `RegExp`
+ `Error`

While there is a difference, for example, between a primitive `string` and a `String` object, js automatically coerces a `string` primitive to a `String` object where necessary to perform operations on it:

```js
var strPrimitive = "I am a string";

console.log( strPrimitive.length );			// 13

console.log( strPrimitive.charAt( 3 ) );	// "m"
```

### Object Properties

### Accessing Properties

To access the value at the location `a` in `myObject` we need to use wither the `.` operator (property access) or the `[]` operator (key access).

The main difference is that the `.` operator requires an `Identifier` compatible property name after it, where as `[".."]` syntax can basically take any UTF-8 encoded compatible string.

### Computed Property Names

The `myObject[..]` property access syntax is useful if you need to use a computed expression value as the key name.... BUT!  es6 adds _computed property name_ where you can specify an expression, surrounded by a `[ ]` pair, in the key-name position of an object-literal declration:

```js
var myObject = {
	[Symbol.Something]: "hello world"
};
```

### Arrays

Arrays also use the `[ ]` access form but have slightly more structure organization for how and where the values are store.  They assume _numeric indexing_ which means that values are stored in locations, usually called _indicies_, at non-negative integers, such as `0` and `42`

Don't forget, arrays _are_ objects so even though each index is a positive integer, you can _also_ add properties onto the array:

```js
var myArray = [ "foo", 42, "bar" ];

myArray.baz = "baz";

myArray.length;	// 3

myArray.baz;	// "baz"
```

>Notice that adding named properties (regardless of . or [ ] operator syntax) does not change the reported length of the array.

If you try to add a property to an array, but the property name looks like a number, it will end up instead as a numeric index (thus modifying the array contents):

### Duplicating Objects

Not easy... How far do we duplicate properties that reference other objects?

ES6 has now defined `Object.assign(..)` for this task. `Object.assign(..)` takes a _target_ object as its first parameter, and one or more _source_ objects as its subsequent parameters.  It iterates over all the _enumerable_, _owned keys_ (immediately present) on the _source_ object(s) and copies them (via `=` assignment only) to _target_.  It also, helpfully, returns _target_, as you can see below:

```js

function anotherFunction() { /*..*/ }

var anotherObject = {
	c: true
};

var anotherArray = [];

var myObject = {
	a: 2,
	b: anotherObject,	// reference, not a copy!
	c: anotherArray,	// another reference!
	d: anotherFunction
};

anotherArray.push( anotherObject, myObject );

var newObj = Object.assign( {}, myObject );

newObj.a;                       // 2
newObj.b === anotherObject;	// true
newObj.c === anotherArray;	// true
newObj.d === anotherFunction;	// true
```

>__Note:__ In the next section, we describe "property descriptors" (property characteristics) and show the use of `Object.defineProperty(..)`. The duplication that occurs for `Object.assign(..)` however is purely `=` style assignment, so any special characteristics of a property (like `writable`) on a source object are not preserved on the target object.

### Property Descriptors

As of es5, all properties are described in terms of a __property descriptor__.

```js
var myObject = {
	a: 2
};

Object.getOwnPropertyDescriptor( myObject, "a" );
// {
//    value: 2,
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```

While we can see what the default values for the property descriptor characteristics are when we create a normal property, we can use `Object.defineProperty(..)` to add a new property, or modify an existing one (if it's `configurable`!), with the desired characteristics.

```js
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true,
	configurable: true,
	enumerable: true
} );

myObject.a; // 2
```

### Immutability

It is sometimes desired to make properties or objects that cannot be changed (either by accident or intentionally). ES5 adds support for handling that in a variety of different nuanced ways.

It's important to note that all of these approaches create shallow immutability. That is, they affect only the object and its direct property characteristics. If an object has a reference to another object (array, object, function, etc), the contents of that object are not affected, and remain mutable.

#### Object Constant

By combining `writable:false` and `configurable:false`, you can essentially create a constant (cannot be changed, redefined or deleted) as an object property, like:

```js
var myObject = {};

Object.defineProperty( myObject, "FAVORITE_NUMBER", {
	value: 42,
	writable: false,
	configurable: false
} );
```

#### Prevent Extensions

If you want to prevent an object from having new properties added to it, but otherwise leave the rest of the object's properties alone, call `Object.preventExtensions(..)`:

```js
var myObject = {
	a: 2
};

Object.preventExtensions( myObject );

myObject.b = 3;
myObject.b; // undefined
```

#### Seal

`Object.seal(..)` creates a "sealed" object, which means it takes an existing object and essentially calls `Object.preventExtensions(..)` on it, but also marks all its existing properties as `configurable:false`.

So, not only can you not add any more properties, but you also cannot reconfigure or delete any existing properties (though you can still modify their values).

#### Freeze

`Object.freeze(..)` creates a frozen object, which means it takes an existing object and essentially calls `Object.seal(..)` on it, but it also marks all "data accessor" properties as `writable:false`, so that their values cannot be changed.

This approach is the __highest level of immutability that you can attain for an object itself__, as it prevents any changes to the object or to any of its direct properties (though, as mentioned above, the contents of any referenced other objects are unaffected).

You could "deep freeze" an object by calling `Object.freeze(..)` on the object, and then recursively iterating over all objects it references (which would have been unaffected thus far), and calling `Object.freeze(..)` on them as well. Be careful, though, as that could affect other (shared) objects you're not intending to affect.

#### Existence

We can ask an object if it has a certain property without asking to get that property's value:

```js
var myObject = {
	a: 2
};

("a" in myObject);		// true
("b" in myObject);		// false

myObject.hasOwnProperty( "a" );	// true
myObject.hasOwnProperty( "b" );	// false
```

The `in` operator will check to see if the property is in the object, or if it exists at any higher level of the `[[Prototype]]` chain object traversal. By contrast, `hasOwnProperty(..)` checks to see if only myObject has the property or not, and will not consult the `[[Prototype]]` chain.

#### Enumerable

`propertyIsEnumerable(..)` tests whether the given property name exists directly on the object and is also `enumerable:true`.

`Object.keys(..)` returns an array of all enumerable properties, whereas `Object.getOwnPropertyNames(..)` returns an array of all properties, enumerable or not.

`Object.keys(..)` and `Object.getOwnPropertyNames(..)` both inspect only the direct object specified and won't climb the prototype chain.

#### Iteration

The `for..in` loop iterates over the list of enumerable properties on an object (including its `[[Prototype]]` chain). But what if you instead want to iterate over the values?

With numerically-indexed arrays, iterating over the values is typically done with a standard `for` loop, like:

```js
var myArray = [1, 2, 3];

for (var i = 0; i < myArray.length; i++) {
	console.log( myArray[i] );
}
// 1 2 3
```

ES5 also added several iteration helpers for arrays, including `forEach(..)`, `every(..)`, and `some(..)`. Each of these helpers accepts a function callback to apply to each element in the array, differing only in how they respectively respond to a return value from the callback.

+ `forEach(..)` will iterate over all values in the array, and ignores any callback return values. 
+ `every(..)` keeps going until the end or the callback returns a false (or "falsy") value.
+ `some(..)` keeps going until the end or the callback returns a true (or "truthy") value.

These special return values inside `every(..)` and `some(..)` act somewhat like a break statement inside a normal for loop, in that they stop the iteration early before it reaches the end.

If you iterate on an object with a `for..in` loop, you're also only getting at the values indirectly, because it's actually iterating only over the enumerable properties of the object, leaving you to access the properties manually to get the values.

But what if you want to iterate over the values directly instead of the array indices (or object properties)? Helpfully, ES6 adds a `for..of` loop syntax for iterating over arrays (and objects, if the object defines its own custom iterator):

```js
var myArray = [ 1, 2, 3 ];

for (var v of myArray) {
	console.log( v );
}
// 1
// 2
// 3
```