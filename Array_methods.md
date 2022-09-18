# ES5 Features

## Array.map

The `Array.map` method does the job of transforming an array into another array of the same length. In mathematical terms the methods maps a transformation over a set, resulting into a new set of the same cardinality. The method is passed a callback function, each element is passed as argument to the callback function and the return values of these executed callback are concatenated into an array.

```js
const oneToFive = [1, 2, 3, 4, 5];
const addTen = (n) => n + 10;

// map method
let elevenToFifteen = oneToFive.map(addTen);
console.log(elevenToFifteen); // [11, 12, 13, 14, 15]

// same effect can be achieved using for loop
elevenToFifteen = [];
for (let num of oneToFive) {
  elevenToFifteen.push(addTen(num));
}
console.log(elevenToFifteen); // [11, 12, 13, 14, 15]

// can accept callback functions of other types
const oneTwoThree = [1, 2, 3];
const heading_tags = oneTwoThree.map((n) => '<h' + n + '>Hello</h' + n + '>');
console.log(heading_tags); // ['<h1>Hello</h1>', '<h2>Hello</h2>', '<h3>Hello</h3>']
// notice that an array of numbers are being converted into an array of strings
```

```js
// initialize an array with ten zeros.
const tenZeros = Array(10).fill(0);
console.log(tenZeros); // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

// creating a new array with each zero replaced by its index
// the second argument being passed is the corresponding index
// of the value being passed
const zeroToNine = tenZeros.map((currentValue, currentIndex) => currentIndex);
console.log(zeroToNine); // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

// creating a new array with each element in the previous array doubled in value
let doubleZeroToNine = zeroToNine.map((currentValue) => 2 * currentValue);
console.log(doubleZeroToNine); // [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
// zeroToNine -> doubleZeroToNine
// 0          -> 0
// 1          -> 2
// 2          -> 4
// 3          -> 6
// ...        -> ...
// 9          -> 18

// the function passed to the method map can be defiend as
const double = (n) => 2 * n;
doubleZeroToNine = zeroToNine.map(double);
console.log(doubleZeroToNine); // [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

## Array.filter

The `Array.filter` method forms a new array which will be a subset of the old array. Thus this method is able to filter out elements out of the array. Just like `Array.map` method, filter accepts a callback function as its argument. The return value of this callback should have a truthy value i.e. it should be a boolean value or it will be coersed into a boolean value. The element, when passed as a argument to the callback, returns `true` then it is kept in the new array else it will be discarded.

```js
const isPositive = (n) => n > 0;
const integers = [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5];

// Using the Array.filter method
let positiveIntegers = integers.filter(isPositive);
console.log(positiveIntegers); // [1, 2, 3, 4, 5]

// same effect can be achieved using for loop
positiveIntegers = [];
for (let num of integers) {
  if (isPositive(num)) {
    positiveIntegers.push(num);
  }
}
console.log(positiveIntegers); // [1, 2, 3, 4, 5]
```

## Array.reduce

The `Array.reduce` method is different from the former methods as it can return types other than arrays. It accepts a very particular callback which accpets atleast two arguments, `accumulator` and `currentValue`. This callbacks return value acts as the next `accumulator`. Thus in the end the method returns the last accumulator.

```js
const add = (accumulator, currentValue) => accumulator + currentValue
const zeroToNine = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
const initialValue = 0

const sum = zeroToNine.reduce(add, );
console.log(sum) // 45

// same thing can be done with a for loop
let accumulator = initialValue;
for (let currentValue of zeroToNine) {
  accumulator = add(accumulator, currentValue);
}
console.log(accumulator); // 45
```

Let us look at the state of this example at every iteration

| accumulator | currentValue | add(...) |
|-------------|--------------|----------|
| 0           | 0            | 0        |
| 0           | 1            | 1        |
| 1           | 2            | 3        |
| 3           | 3            | 6        |
| 6           | 4            | 10       |
| 10          | 5            | 15       |
| 15          | 6            | 21       |
| 21          | 7            | 28       |
| 28          | 8            | 36       |
| 36          | 9            | 45       |

In the previous example, the array was reduced into a number. This method can convert arrays into other types as well like string, object and array themself even with differnt shapes. For example we can implement concat function which takes an array of arrays and transforms it into a single array.

```js
const matrix3x3 = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9],
];
const initialValue = [];
const append = (accumulator, currentValue) => [...accumulator, ...currentValue];

// Using Array.reduce
const oneToNine = matrix3x3.reduce(append, [...initialValue]);
console.log(oneToNine) // [1, 2, 3, 4, 5, 6, 7, 8, 9]

// Using for loop
// making a copy of `initialValue`
let accumulator = [...initialValue];
for (let currentValue of matrix3x3) {
  accumulator = append(accumulator, currentValue);
}
console.log(accumulator) // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Let us look at the state of this example at every iteration

| accumulator          | currentValue | append(...)                 |
|----------------------|--------------|-----------------------------|
| []                   | [1, 2, 3]    | [1, 2, 3]                   |
| [1, 2, 3]            | [4, 5, 6]    | [1, 2, 3, 4, 5, 6]          |
| [1, 2, 3, 4, 5, 6]   | [7, 8, 9]    | [1, 2, 3, 4, 5, 6, 7, 8, 9] |

# References

1. More on higher order functions : https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function
2. Array.map : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map
3. Array.filter : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter
4. Array.reduce : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce