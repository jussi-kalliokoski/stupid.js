# Stupid JS Style Guide

It's not like we need yet another coding style guide, but I decided to make one anyway to help people contribute to my projects, so here we go.

## Stupid JS?!

Yes, the name is how I think Douglas Crockford would name my style guide. You're free to judge for yourself, or fork if you don't like it. To make things easier for you, I've made a tool called [asifier](https://github.com/jussi-kalliokoski/asifier) that does the work for you (the JS engine) and tells where to automatically insert the semicolons and even inserts them for you if you want.

## Y U NO ½:

Coming from C initially, I used to think that ASI was evil, but some time ago I decided to just give up and learn how it works. It's not that complicated really. I've found that after adopting a coding style with no (few) semicolons, the missing semicolon bugs/errors I used to get have disappeared. It's easy to forget a semicolon when it's supposed to be there, but if you structure your code in a way that semicolons make no difference, there's nothing to forget.

This is not a choice of laziness, however; at some things you'll end up typing more so it's about a zero sum.

## Basics

Everything in this coding style aims at consistency. If something you think something isn't consistent, please file an issue.

### Control structures with blocks

Control structures follow the pattern of "control structure name" followed by a space, followed by parameters, followed by a space, followed by the block. This separates control structures from function calls. To illustrate:

```javascript

/* if statement */
if (true) {
	// Do something
} else if (false) {
	// Do something
} else {
	// Do something
}

/* anonymous function */
var a = function (b, c, d) {
	// Do something
}

/* named function */
function a (b, c, d) {
}

/* try-catch */
try {
	// Do something
} catch (e) {
	// Do something
} finally {
	// Do something
}

/* loops */

do {
	// Something
} while (true)

while (true) {
	// Do something
}

for (var i=0; !i; i++) {
	// Do something
}

/* case switching */
switch (a) {
}

/* with */
with (eval) {
	explode(everything),!!!!!!!!1
}

```

#### Conditionals

Avoid using ternary or boolean expressions unless you're going to use the value. Even then, avoid long ternary expressions. It's also mildly OK to drop the braces for really simple things. For example:

```javascript

/* bad */

true ?
	doSomething()
:
	doSomethingElse()

/* good */

if (true) {
	doSomething()
} else {
	doSomethingElse()
}

var a = true ? 'a' : 'b'

/* bad */

true && doSomething()
false && doSomethingElse()

/* good */
if (true) doSomething()
if (false) doSomethingElse()

var a = true && doSomething()
var b = true && doSomethingElse()

```

#### Functions

Functions are the key tools for reusable code. As such, you should use them a lot. Your functions should have simple actions and take parameters defined by this task. If you're having trouble thinking for a name for a function, chances are that one of these two assertions is valid:
 * Your function doesn't do anything.
 * Your function does too many things.

```javascript

/* a good function */
/**
 * Kills an Ape.
 *
 * @arg {Ape} ape The Ape to be killed.
*/
var killApe = function (ape) {
	ape.health = 0
	ape.dead = true
}

/* a bad function */
/**
 * Kills an Ape, gives its food to another Ape, shows the Player a notification after a specified time.
 *
 * @arg {Ape} ape The Ape to be killed.
 * @arg {Ape} otherApe The other Ape to give to give the first Ape's food to.
 * @arg {Number} notificationDelay The delay after which to show the notification.
*/
var killApeGivingFoodAndShowingANotification = function (ape, otherApe, notificationDelay) {
	/* ... */
}

```

#### Loops

Nothing special for these, use your better judgement on how to whitespace the loop's arguments. Readability is the most important thing here.

#### Case Switching

Nothing really special here either. Avoid fallthroughs if possible. Don't indent the case statements, it's just an unnecessary level of indentation:

```javascript
switch (num) {
case 1:
	break
case 2:
	break
default:
	/* ... */
}
```

#### With Statement

See the section about [Chaining](#chaining).

### Indentation / Whitespace

Tabs vs. spaces is sort of liberal, but if the project uses 2 spaces, use 2 spaces, if it uses tabs, use tabs and so on.

Whatever you do, don't:
 * Mix tabs and spaces.
 * Use tabs if you're not using the default size (eight).
 * Have trailing whitespace. (To fix, try "s/\s+$//")
 * Have lines wider than 80 columns.

Indentation is useful. It tells you when your code is no longer readable. If you've went over the 80 columns, it's likely that you have too much indentation (=spaghetti) going on, or maybe it's bad RegEx day. See the section titled "[Return Early, Return Often](#return-early-return-often)" for some tips on how to reduce the level of indentation.

### Return Early, Return Often

This is an important philosophy to grasp in order to keep your code readable. For example, instead of having two nested control structures before getting to the actual action, consider if you could get rid of one or more of them by breaking out of the control structure. To illustrate:

```javascript

/* not so good */

var keys = function (obj) {
	var keyArray = []
	var hasOwn = Object.prototype.hasOwnProperty

	for (var key in obj) {
		/* if the key is own, add it to result */
		if (hasOwn.call(obj, key)) {
			keyArray.push(key)
		}
	}

	return keyArray
}

/* better */

var keys = function (obj) {
	var keyArray = []
	var hasOwn = Object.prototype.hasOwnProperty

	for (var key in obj) {
		/* skip if the key isn't own */
		if (!hasOwn.call(obj, key)) continue

		keyArray.push(key)
	}

	return keyArray
}

```

See [this post](http://vocamus.net/dave/?p=1421) by David Humphrey for more examples.

### Chaining

Chaining is bad. Don't do it. Return "this" only when "this" is something created in the function. Chaining hurts readability.

A rule to every exception is hacks. Examples of hacks:
 * a 1k demo
 * tests

### Defining

For clarity and being friendly towards version control, define each variable on its own line. This way you can easily go back and change things without affecting other parts of the code.

Hoisting is fine for small functions, but it suffers from the same readability problem as chaining; you don't want to scroll up to see if you've defined something yet or not. However, avoid defining more than one thing in the arguments of a control structure.

Here are a few examples on how to define things:

```javascript

/* variables */
var a = 1
var b = 2

/* local functions */
var doSomething = function () {
	/* ... */
}

/* class */
function MyClass (a, b) {
	/* initialize properties */
	this.a = a == null ? MyClass.defaultA : a
	this.b = b == null ? 2 : b
}

/* static property */
MyClass.defaultA = 1

/* static class method */
MyClass.doSomething = function () {
	/* ... */
}

/* inheritance */
MyClass.prototype = new AnotherClass()
	/* ... */

/* class method */
MyClass.prototype.doSomething = function () {
	/* ... */
}

```

### Classes

The important part about "Classes" is that you define all their properties in the constructor (or a function that is always called by the constructor). The idea is that whereas plain objects in JavaScript can change shape as you go and so on, you should make your classes have an identical, static structure, as if you were defining a class in a static language. This means that for any possible instances of the same class, at any time, Object.keys(instance) should be identical.

As for methods, these are not properties, define them in the prototype.

These are partly because of performance, but the important part is that this makes things easy to find. Methods in the prototype, properties in the constructor.

If you have classes that have many properties you may or may not want as arguments of the constructor, consider using a single object/map argument instead, in combination with a default values object. This preserves the aforementioned instance symmetry. Illustration:

```javascript

function MyClass (args) {
	extend(this, MyClass.defaults, args)
}

MyClass.defaults = {
	a: 1,
	b: 2,
	c: 4
}

```

See the section titled "[Defining](#defining)" for more information.

### IIFE / Closures / Scope

For scoping purposes, use closures. If a JS file consists of only one wrapping closure, adding indentation for this is pointless. An example of how to use IIFEs:

```
/* all-file wrapper, no need for indentation.
void function () {

var a = 1
var b = 2
/* inline IIFE, i.e. nested scoping. Add indentation.
var c = function (w) {
	return w.a || w.b || w.c
}(window)

}()
/* EOF */
```

### Expressions / Statements

In the part about [closures](#iife-closures-scope), you probably noticed that I used two different forms of immediately invoked functions. This is because they were two different things. The first, the wrapping closure, was a statement. It's not supposed to return a value, so it's prefixed with a "void". The other one does return a value that's assigned to something, so it's an expression.

This expression / statement separation helps you avoid mistakes and typos from causing harmful side effects. A good rule for this is to never start a line with a bracket or a parens. The ++/-- operators are also expressions, so you should use them as such.

Examples:

```javascript
/* bad:
 * This tries to execute the return value
 * of the first IIFE with the return value
 * of the second one.
*/
(function () {
}())

(function () {
}())

/* better */

void function () {
}()

void function () {
}

/* bad:
 * The brackets act as an index operator,
 * resulting in "can't call .forEach of undefined".
*/

var b = 1
var a = b
[1,2,3].forEach(...)

/* better */

var b = 1
var a = b
void [1,2,3].forEach(...)

```

## Embrace the Warts

If you take one thing away with you from this style guide, let it be this part.

JavaScript has a lot of design flaws, but there's no point in trying to hide from them. The more you try to look the other way, the more they will bite you when you least expect it. Learn the language. Not just the good parts. The bad parts too. Embrace the warts, they're here to stay.

The sooner you accept that, the sooner you can let go of your arbitrary rules that are in place to keep you from having to understand how the language works. My coding style is not a something I've designed. It's something I've accidentally arrived to, something that I've found makes me produce the least bugs I can. With bugs, I mean bugs, not errors. Nowadays, if I make a typo or a mistake, it most likely results in a syntax error, rather than an ugly side effect where you replace your database with undefined.

I'm hoping that sharing this guide helps you contribute to my projects and possibly even better, inspires you to learn the language that is JavaScript.

## Disclaimer

Note that this is a living standard.
