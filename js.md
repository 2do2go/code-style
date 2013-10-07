
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


## Use `this` only in object constructors, methods, and in setting up closures

The semantics of this can be tricky. At times it refers to the global object (in most places), the scope of the caller (in eval), a node in the DOM tree (when attached using an event handler HTML attribute), a newly created object (in a constructor), or some other object (if function was called or applyed).

Because this is so easy to get wrong, limit its use to those places where it is required:
* in constructors
* in methods of objects (including in the creation of closures)


## Don`t use multiline string literals

Do not do this:

```js
var myString = 'A rather long string of English text, an error message \
                actually that just keeps going and going -- an error \
                message to make the Energizer bunny blush (right through \
                those Schwarzenegger shades)! Where was I? Oh yes, \
                you\'ve got an error and all the extraneous whitespace is \
                just gravy.  Have a nice day.';
```

The whitespace at the beginning of each line can't be safely stripped at compile time; whitespace after the slash will result in tricky errors; and while most script engines support this, it is not part of ECMAScript.

Use string concatenation instead:

```js
var myString = 'A rather long string of English text, an error message ' +
    'actually that just keeps going and going -- an error ' +
    'message to make the Energizer bunny blush (right through ' +
    'those Schwarzenegger shades)! Where was I? Oh yes, ' +
    'you\'ve got an error and all the extraneous whitespace is ' +
    'just gravy.  Have a nice day.';
```

## Use Array and Object literals

Always use the more readable Array and Object literals instead of Array and Object constructors:

```js
var a = [x1, x2, x3];
var a2 = [x1, x2];
var a3 = [x1];
var a4 = [];

var o = {};
var o2 = {
  a: 0,
  b: 1,
  c: 2,
  'strange key': 3
};
```

# Used materials

* [Google JavaScript Style](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
* [DailyJS: JavaScript and Semicolons](http://dailyjs.com/2012/04/19/semicolons/)
