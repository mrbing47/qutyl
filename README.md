# Introduction

This is a repository containing some of the useful util functions.

## Install

To use this library in your code,

```bash
npm i qutyl
```

However, you can install the package from any other package-manager (like yarn, bun, etc.) which forks from NPM Registry.

---

## addParent

This function creates a **deep copy** and adds the reference to the parent object in `this` for function to access the object tree.

#### Usage

```Javascript

const { addParent } = require("qutyl");

const obj = {
	a: 10,
	foo: {
		bar(){
			console.log(this.parent.a);
		}
	}
}

const newObj = addParent(obj);

newObj.foo.bar();
// 10
```

## assert

This is a function accepts a predicate function and returns a function accepting _value_ and _...message_ and throws an **error** if predicate is returns true with _value_ or returns the _value_.

#### Usage

```Javascript
const { assert } = require("qutyl");

const assertFalsey = assert((value) => !!value);

try{
	assertFalsey("", "Error: A falsey value has been passed.");
}catch(error){
	console.log(error.message);
	// Error: A falsey value has been passed.
}

const assertUndefined = assert((value) => value == undefined);

try{
	assertUndefined(undefined, "Error: An undefined value has been passed.");
}catch(error){
	console.log(error.message);
	// Error: An undefined value has been passed.
}

try{
	let value = assertUndefined(10, "Error: Can not perform binary operation on undefined.");
	console.log(value);
	// 10
}catch(error){
	console.log(error.message);
}
```

## compare

This function performs a deep **SameValueZero** check on all the values of an object and array or on a simple primitive variable. For object type, it checks for keys and values, for arrays, it first sorts the values and then compares the values, for other, **SameValueZero** is used.

#### Flags

In order to tune the the comparision according to your needs, use the following flags:

```Javascript
const { compare } = require("qutyl")

compare.TYPE
// Only checks for the type of two values.

compare.LENGTH
// For objects, arrays and string, first checks the type and then it checks for length, skipping the elements comparision.

compare.SKIP.SORT
// For arrays, the function first sorts them in order to compare them, but if you have know that passed arguments are sorted, use this flag to skip the sorting process.
```

#### Usage

```Javascript
const { compare } = require("qutyl");

const foo = {
	a: 1,
	wow: {
		x: {
			y: {
				z: 20
			}
		}
	}
};

const temp = {
	a: 1,
	wow: {
		x: {
			y: {
				z: 20
			}
		}
	}
};

const bar = {
	b: 2,
	e: 10
};

const blah = {
	c: 2,
	d: 3,
	f: 20
};



compare(foo, bar);
// false

compare(foo, temp);
// true

compare(blah, bar);
// false

compare(foo, bar, compare.TYPE);
// true

compare(foo, bar, compare.LENGTH);
// true

compare(foo, blah, compare.LENGTH);
// false


```

```Javascript
const { compare } = require("qutyl");

const foo = [1,2,3]
const bar = [1,2,3,4]
const blah = [2,3,1]
const wow = {
	a: 2
}

compare(foo, bar);
// false

compare(foo, bar, compare.SKIP.SORT); // does not influence the result but saves extra processing time.
// false

compare(foo, bar, compare.TYPE);
// true

compare(foo, bar, compare.LENGTH);
// false

compare(foo, blah);
// true

compare(foo, wow, compare.TYPE);
// false
```

```Javascript
const { compare } = require("qutyl");

compare(10, "10");
// false

compare(10, 10);
// true

compare(10, 1);
// false

compare(10, 1, compare.TYPE);
// true

compare("hello", "world")
// false

compare("hello", "world", compare.LENGTH)
// true

compare(NaN, NaN)
// true

compare(+0, -0)
// true
```

## groupBy

This function returns an **Object** which contains the classes (defined by passing each element in _classifier_) containing the elements of the array.

> **NOTE:** This has been implemented as **Object.groupBy()** in most browsers but not supported by some runtimes.

#### Usage

```Javascript
const { groupBy } = require("qutyl");

const array = [1,2,3,4,5,6,7,8,9,0];

const classifier = (element) => element % 2 ? "odd" : "even";

groupBy(array, classifier);
// {odd: [1,3,5,7,9], even: [2,4,6,8,0]}
```

## merge

This function merges the _...args_ arrays passed to it based on the value returned by _evaluator()_.

#### Usage

```Javascript
const { merge } = require("qutyl");

const foo = [2,3,4,7,9];
const bar = [0,1,8];
const blah = [5,6];

const evaluator = (element) => element;

merge(evaluator, foo, bar, blah)
// [0,1,2,3,4,5,6,7,8,9]
```

## range

This function returns an array containing the elements from including _start_ to excluding _end_ where each element is incremented by _steps_ (default = 1) which should not be equal to 0.

This function takes either `(end)(start = 1, steps = 1)` or `(start, end)(steps = 1)` or `(start, end, steps)`.

> **NOTE:** This is a mimic to the Python **range()**, however returns an array instead of an iterator.

#### Usage

```Javascript
const { range } = require("qutyl");

range(10);
// [0,1,2,3,4,5,6,7,8,9]

range(-10);
// []

range(-10, 0);
// [ -10, -9, -8, -7, -6, -5, -4, -3, -2, -1 ]

range(0, -10);
// []

range(0, -10, -1);
// [ 0, -1, -2, -3, -4, -5, -6, -7, -8, -9 ]

range(5, 10);
// [5, 6, 7, 8, 9]

range(5, 20, 4);
// [ 5, 9, 13, 17 ]

range(0, 10, 0);
// []
```

## set

This contains all the relevant `Set` functions.

#### at

This function returns the element from a `Set()` from the integer _index_, i.e., it supports both forward(positive) and backward(negative) indexing.

###### Usage

```Javascript

const { set: { at } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);

at(foo, -1);
// 4

at(foo, -2);
// 3

at(foo, 2);
// 2

at(foo, 10);
// undefined
```

#### difference

Accepts either an `Array` or `Set` and returns a `Set` containing all the element that are unique to the _first_ argument. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript

const { set: { difference } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);

difference(foo, bar);
// Set([3,4])

difference(bar, foo);
// Set([5,6,7,8,9])
```

#### intersection

Accepts either an `Array` or `Set` and returns a `Set` containing all the element that are common in both arguments. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript

const { set: { intersection } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);

intersection(foo, bar);
// Set([0,1,2])
```

#### isDisjointFrom

Accepts either an `Array` or `Set` and returns a `boolean` where if _both_ arguments have no common elements then **true**, else **false**. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript
const { set: { isDisjointFrom } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);
const blah = new Set([5,6,7,8,9]);

isDisjointFrom(foo, bar);
// false

isDisjointFrom(foo, blah);
// true
```

#### isSubsetOf

Accepts either an `Array` or `Set` and returns a `boolean` where if all the elements in _first_ argument are present in the _second_ argument then **true**, else **false**. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript

const { set: { isSubsetOf } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);
const blah = new Set([5,6,7,8,9]);

isSubsetOf(foo, bar);
// false

isSubsetOf(bar, foo);
// false

isSubsetOf(foo, blah);
// false

isSubsetOf(blah, bar);
// true

isSubsetOf(bar, blah);
// false
```

#### isSupersetOf

Accepts either an `Array` or `Set` and returns a `boolean` where if _first_ argument contains all the elements from the _second_ argument, then **true**, else **false**. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript

const { set: { isSupersetOf } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);
const blah = new Set([5,6,7,8,9]);

isSupersetOf(foo, bar);
// false

isSupersetOf(bar, foo);
// false

isSupersetOf(foo, blah);
// false

isSupersetOf(blah, bar);
// false

isSupersetOf(bar, blah);
// true
```

#### symmetricDifference

Accepts either an `Array` or `Set` and returns a `Set` containing all the element that are unique to _both_ arguments and not the common ones. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript
const { set: { symmetricDifference } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);
const blah = new Set([5,6,7,8,9]);

symmetricDifference(foo, bar);
// Set([3,4,5,6,7,8,9])

symmetricDifference(foo, blah);
// Set([0,1,2,3,4,5,6,7,8,9])

symmetricDifference(bar, blah);
// Set([0,1,2])
```

#### union

Accepts either an `Array` or `Set` and returns a `Set` containing all the element from _both_ arguments after removing the duplicates. If the either argument is not of type `Array` or `Set`, it will return **undefined**.

###### Usage

```Javascript
const { set: { union } } = require("qutyl");

const foo = new Set([0,1,2,3,4]);
const bar = new Set([0,1,2,5,6,7,8,9]);

union(foo, bar);
// Set([0,1,2,3,4,5,6,7,8,9])
```

## shuffle

This function accepts an array and returns a new array with all the elements shuffled. If data type is other than _array_, it will return _undefined_.

#### Usage

```Javascript

const foo = [1, 2, 3, 4, 5, 6, 7]

shuffle(foo)
// [ 2, 7, 4, 6, 3, 5, 1 ]


```

## zip

This function zips each element of the passed _...arrays_ and returns the resultant array with size of the smallest array in _arrays_.

#### Usage

```Javascript

const { zip } = require("qutyl");

const foo = [0,1,2,3,4];
const bar = [0,1,2,5,6,7,8,9];
const blah = [5,6,7,8,9];

zip(foo, bar, blah);
/*
[
  [ 0, 0, 5 ],
  [ 1, 1, 6 ],
  [ 2, 2, 7 ],
  [ 3, 5, 8 ],
  [ 4, 6, 9 ]
]
*/
```

## zipAll

This function zips each element of the passed _...arrays_ and returns the resultant array with size of the largest array in _arrays_ and replaces the missing elements for smaller arrays with **undefined**.

#### Usage

```Javascript
const { zipAll } = require("qutyl");

const foo = [0,1,2,3,4];
const bar = [0,1,2,5,6,7,8,9];
const blah = [5,6,7,8,9];

zipAll(foo, bar, blah);
/*
[
  [ 0, 0, 5 ],
  [ 1, 1, 6 ],
  [ 2, 2, 7 ],
  [ 3, 5, 8 ],
  [ 4, 6, 9 ],
  [ undefined, 7, undefined],
  [ undefined, 8, undefined ],
  [ undefined, 9, undefined ]
]
*/
```
