
# JavaScript code style

## Language Rules

### Always use semicolons

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


### Don't use primitive types wrapper objects for assign value to variable

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


### Method and property definitions

Define instance properties within contructor, prototype properties and methods as prototype members:

```js
function Foo() {
	this.someInstanceProperty = 1;
}
Foo.prototype.somePrototypeProperty = '1';
Foo.prototype.somePrototypeMethod = function() {
};
```

### Inheritance

Use "Parasitic Combination Inheritance" e.g. `util.inherits` in node.js

```js
function ChildConstructor(options) {
  // call parent contructor
  ParentConstructor.call(this, options);
}
util.inherits(ChildConstructor, ParentConstructor);
ChildConstructor.prototype.someMethod = function(a, b) {
  // call parent method
  ParentConstructor.prototype.someMethod.call(this, a, b);
};
```

It also can be easily implemented using `Object.create` JavaScript function (in
case when your JavaScript environment doesn't provides function for inheritance).

But don't use link to parent contructor which some function for inheritance provide,
e.g. node.js `util.inherits` provides link to parent constructor as `super_` property.
Using such link within constructor at several inheritance levels leads to infinite recursion.
Don't do it. Just call parent constructor or method using `call` or `apply` as shown above instead.

### Use `this` only in object constructors, methods, and in setting up closures

The semantics of this can be tricky. At times it refers to the global object (in most places), the scope of the caller (in eval), a node in the DOM tree (when attached using an event handler HTML attribute), a newly created object (in a constructor), or some other object (if function was called or applyed).

Because this is so easy to get wrong, limit its use to those places where it is required:
* in constructors
* in methods of objects (including in the creation of closures)


### Don't use multiline string literals

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

### Use Array and Object literals

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

### Don't modify prototypes of builtin objects

Modifying builtins like Object.prototype and Array.prototype are strictly forbidden. Modifying other builtins like Function.prototype is less dangerous but still leads to hard to debug issues in production and should be avoided.


### `'use strict';` mode

Strict mode has following benefits

* Forces you to declare variables with `var`
* Prevents function declarations within blocks
* Prohibits duplicate property names at object, dublicate argument names
* Strict mode makes `arguments`, `eval` and `this` (the specified this is used unchanged, but if unspecified, this will be `undefined`) less bizarrely magical
* Paving the way for future ECMAScript versions (`implements`, `interface` and others become reserved keywords, etc)

[and more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/Strict_mode)


### Don't use `eval`

`eval` should be used only in specific cases e.g. code loader.

`eval` makes for confusing semantics and is dangerous to use if the string being
eval'd contains user input. There's usually a better, clearer, and safer way to
write your code, so its use is generally not permitted.

For RPC you can always use JSON and read the result using `JSON.parse` instead of `eval`.


### Don't use `with`

Using with clouds the semantics of your program. Because the object of the with
can have properties that collide with local variables, it can drastically change
the meaning of your program. For example:

```js
var obj = {x: 1};
var x = 2;

with (obj) {
  // prints 1
  console.log(x);
  delete obj.x;
  // prints 2
  console.log(x);
}
```

## Used materials

* [Google JavaScript Style](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
* [Strict mode - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/Strict_mode)
* [DailyJS: JavaScript and Semicolons](http://dailyjs.com/2012/04/19/semicolons/)
