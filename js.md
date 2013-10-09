
# JavaScript code style

## Language rules

### Always use semicolons

Relying on implicit semicolon insertion (Automatic Semicolon Insertion - ASI) can
cause subtle, hard to debug problems. Don't do it. You're better than that.

There are a couple places where missing semicolons are particularly dangerous:

```js
// example 1
a = b + c
[1].push(a)

// example 2
a = b + c
(options || {}).foo ? bar() : baz()
```

In both of these cases, the parser doesn’t need to apply ASI in order to have
properly formed code. In the first example, it can ignore the new line and treat
the [ as applying to c, likewise in the second example, the ( can apply to c.


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


### Check for truthy or not truthy by default

`false`, `0`, `''`, `null`, `undefined`, `NaN` are falsy values in JavaScript and
it is very simple to check whether it is truthy value or not:

```js
if (val) {
  // if val is truthy
}
if (!val) {
  // if val is falsy
}
if (val && val2 && val3) {
  // if val, val2, val3 are truthy
}
```

it is nice and compact syntax, use it everywhere (e.g. instead of check that
object variable not `null` just check that it is truthy), except cases when you
really need to check for a specific falsy value.


### Cast types manually

Implicit javascript type casting can blow your mind with no effort, e.g.

```js
Boolean(0) == false;
Boolean('') == false;
Boolean(null) == false;
Boolean(undefined) == false;
// falsy values are equal, that's ok
'' == 0;
null == undefined;
// but not all falsy values are equal
0 != null;
```

that's why you better do it yourself, `Boolean`, `Number`, `String` are great for
types casting

```js
Number('10') == 10;
Boolean(0) == false;
String(10) == '10';
```


### Use `==` by default

Since you already cast types manually (see `Cast types manually`) there no reason
to use strict versions of equal (`===`) and not equal (`!==`) operators.
Especially considering the fact that other operators (greater, greater or
equal, etc) doesn't have strict versions. Use strict operators when you really need
them e.g. to distinguish falsy values:

```js
// will be true only for 0
if (index === 0) {
}
```


## Style rules


### Use camelСase

In general, use functionOrMethodsNamesLikeThis, variableNamesLikeThis,
ClassNamesLikeThis, and fileNamesLikeThis.js


### Use tabs for indentation


### Limit line width to 80 characters


### Use single quotes


### Use parentheses only where required

Use sparingly and in general only where required by the syntax and semantics.

Never use parentheses for unary operators such as delete, typeof and void or
after keywords such as return, throw as well as others (case, in or new).


### Start your curly braces on the same line

Because of implicit semicolon insertion, always start your curly braces on the
same line as whatever they're opening. For example:

```js
if (something) {
  // ...
} else {
  // ...
}
```


### Use dot notation by default

Do `obj.key` instead of `obj['key']` except cases when key really have to be
surrounded with quotes e.g. `obj['some key']`


### Quote object keys only when required

Do `obj = {key: 1};` instead of `obj = {'key': 1};` except cases when key really
have to be surrounded with quotes e.g. `obj = {'some key': 1}`


## Used materials

* [Google JavaScript Style](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
* [Strict mode - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/Strict_mode)
* [DailyJS: JavaScript and Semicolons](http://dailyjs.com/2012/04/19/semicolons/)
