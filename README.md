# Functional Programming in JS

This is my notebook. Feel free to use it

_if you spot bug, let me know_ 📣

## Pure Functions

Take a note that functions take argument

- By value:
  1. Strings, numbers etc..
- By reference
  1. Arrays
  1. Objects

Pure Functios are:

- _Clean_:

  1. They do not change input data (and any other data)
  1. They always return the same data for the same input

- _Referencial Transparency_

  1. You can just put data instead of function

- _Memoizable_
  1.  You can save part calculations if you often use them

## High Order Functions

Those are the functions which take functions and return a new one. Instead of make many function which are similar whe may build function like that:

```javascript
const reduce = (reducer, initial, arr) => {
	// implementation of Reduce ( Array Method )
	let result = inital;
	for (let i = 0; i < arr.length; i++) {
		result = reducer(result, arr[i]) ? result : arr[i];
	}
	return result;
};
const filter = (reducer, arr) => reduce(reducer, [], arr);
const shortest = filter((prev, next) => prev.duration < next.duration, songs); // funckja znajdująca element o najmniejszej wartości w duration
const longest = filter((prev, next) => prev.duration > next.duration, songs);
```

Instead of all of this we can just use reduce and do this:

```javascript
const shortestReducer = (prev, next) =>
	prev.duration < next.duration ? prev : next;
const shortest = songs.reduce(shortestReducer, songs);
```

## Function Composition

Image we would like to add and then double the number

```javascript
const double = (y) => y * 2;
const add = (x) => x + 1;
```

To ovoid nesting execution of those functions we can create **Function Composition**

```javascript
const compose = (...fns) => (value) =>
	fns.reduceRight((acc, fn) => fn(acc), value);
```

Keep in mind that now we start execution from right to left

```javascript
add(add(double(2)));
// is equal to
compose(add, add, double);
```

If you would like to execute from left to right use .reduce instead.

## Currying

Take a look at that functions:

```javascript
const multiply = (x, y) => x * y;
```

But what if we would like to use it in map function?

1. We can do something like that
   ```javascript
   const double = multiply.bind(null, 2);
   [2, 10, 15].map(double);
   ```
1. Or use curying instead:

   ```javascript
   const multiply = (x) => (y) => x * y;
   /* 
        or to be more readable
    */
   function multiply(x) {
   	return function y() {
   		return x * y;
   	};
   }
   ```

   Thanks to the scope functiony has acces to multiple _x_.

### Use Case

We will use Ramda library.

```javascript
import { curry, compose, pipe } from "ramda";
function multiply(multiplier, value) {
	return multiplier * value;
}
const number = [1, 2, 4, 10];
const curiedMultiply = curry(multiply);
number.map(curiedMultiply(2)); // [2, 4, 8, 20];
```

Function curry makes currying function from binary function.
We have **strict currying** & **loose currying**

```javascript
curiedMultiply(2, 5) === curiedMultiply(2)(5);
```

So, we ha a list of books:

```javascript
const books = [
    { title: "Total Recall", pages: 593, genre: "biography"},
    ...
]
```

We would like to count number of pages of selected type of books with the help of composition and currying:

```javascript
const hasProperty = curry((property, value, obj) => obj[property] === value);
const getProperty = curry((property, obj) => obj[property]);

const getBusiness = (list) => list.filter(hasProperty("genre", "bussines"));
const getPages = (list) => list.map(getProperty("pages"));
const countPages = (book) => book.reduce((acc, pages) => acc + pages);

pipe(getBusiness, getPages, countPages)(books); // pipe is like composition but calls functions in inverted way (first on left)
```
