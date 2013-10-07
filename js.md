
# JavaScript code style


## Always use semicolons

Relying on implicit insertion can cause subtle (Automatic Semicolon Insertion - ASI), hard to debug problems. Don't do it. You're better than that.

There are a couple places where missing semicolons are particularly dangerous:

```js
// example 1
a = b + c
[1].push(a)

// example 2
a = b + c
(options || {}).foo ? bar() : baz()
```

In both of these cases, the parser doesn’t need to apply ASI in order to have properly formed code. In the first example, it can ignore the new line and treat the [ as applying to c, likewise in the second example, the ( can apply to c. 


## Don`t use primitive types wrapper objects for assign value to variable

There's no reason to use wrapper objects for primitive types, plus they're dangerous:

```js
var x = new Boolean(false);
if (x) {
  alert('hi');  // Shows 'hi', coz typeof x === 'object'
}
```

Don't do it!
However type casting is fine.

```js
var x = Boolean(0);
if (x) {
  alert('hi');  // This will never be alerted, coz typeof x === 'boolean'
}
```

This is very useful for casting things to number, string and boolean.


## Method and property definitions

Define instance properties at contructor, prototype properties and methods as prototype members:

```js
function Foo() {
	this.someInstanceProperty = 1;
}
Foo.prototype.somePrototypeProperty = '1';
Foo.prototype.somePrototypeMethod = function() {	
};
```


# Used materials

* [Google JavaScript Style](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
* [DailyJS: JavaScript and Semicolons](http://dailyjs.com/2012/04/19/semicolons/)
