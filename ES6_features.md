# ES6 Features

## Variable bindings

ES6 provides new ways of declaring variables using the new `let` and `const` keywords which scopes differently than the previously used `var` keyword.

1. var

```js
var one = 1;
```

- Variable scoped to function body.
- Hoisted variable.
- Variable can be reassigned.

2. let

```js
let one = 1;
```

- Variable scoped to block (nearest curly braces).
- Variable isn't hoisted.
- Variable can be reassigned.

3. const

```js
const one = 1;
```

- Variable scoped to block (nearest curly braces).
- Variable isn't hoisted.
- Will throw error on reassignment.

Since `var` allows variables to be scoped to the function body, it can be accessed outside of its block.

```js
function count() {
  for (var i = 0; i < 5; i++) {
    console.log(i);
  }
  console.log(i);
}

count();

// 0
// 1
// 2
// 3
// 4
// 5
```

On the other hand `let` scopes the variable to within the block making it inaccessible from outside of the block. We'll use the same example as above but with the `var` keyword replaced by `let` which results in a error.

```js
function count() {
  for (let i = 0; i < 5; i++) {
    console.log(i);
  }
  console.log(i);
}

count();

// 0
// 1
// 2
// 3
// 4
// Uncaught ReferenceError: i is not defined at count
```

An important point to mention, `var` hoists the variable on to the function body. This simply means the variable is declared as soon as the program flow enters the function body but the variable will not be set to any value until the line `var i = 0;` is reached and will remain undefiend.

```js
function count() {
  console.log(i);
  for (var i = 0; i < 5; i++) {
    console.log(i);
  }
}

count();

// undefiend
// 0
// 1
// 2
// 3
// 4
```

## Arrow Syntax

Javascript allows us to define anonymous function pretty much the same way we define regular functions. The regular function declaration is a statment, thus we can't assign them to a variable or pass them as parameters. We can use anonymus function declaration which are expression rather than statements. Javascript functions are just javascript object. The syntax for anonymus function expression is just a sugar coating on to the actual properties of a function object to make it sweet. Since functions are just objects we can assign them to variables and also pass them as parameters to other functions

```js
// assigning a anonymous function to a variable
const lambda = function () {
  console.log('Lambda functions are cool!');
};
const square = function (number) {
  return number * number;
};
const passArgument10 = function (callback) {
  return callback(10);
};

// these functions can be called like normal functions
lambda(); // Lambda functions are cool!
console.log(square(4)); // 16

// functions can be passed as arguments to other functions
const result = passArgument10(square);
console.log(result); // 100

// functions can also be defiend while passing them as arguments
const output = passArgument10(function (n) { return n * n * n });
console.log(output); // 1000
```

To make this syntax sweeter, new syntax is added to Javascript called arrow functions.

```js
// earlier syntax
const cubeOld = function (n) {
  return n * n * n;
};

// new arrow syntax
const cubeNew = (n) => {
  return n * n * n;
};

console.log(cubeOld(4)); // 64
console.log(cubeNew(4)); // 64

const add2numbers = (x, y) => {
  return x + y;
};
const add3numbers = (x, y, z) => {
  const w = add2numbers(x, y);
  const result = add2numbers(w, z);
  return result;
};

console.log(add2numbers(1, 2)); // 3
console.log(add3numbers(1, 2, 3)); // 6
```

To make this even more compact a couple more rules as added. First, if the `=>` is followed by an expression then the value of that expression is return value for the function being defiend. Second, if there is a single argument then the paranthesis around the argument can be ommitted. This syntax allows us to use Array methods like map, filter and reduce more consicesly.

```js
const cube = n => n * n * n;
const add = (x, y) => x + y;

console.log(cube(7)); // 343
console.log(add(10, 5)); // 15
```

## Spread syntax

The spread syntax allows us to declare object properties from an existing object while defining a new object.

```js
const address = {
  city: 'Pune',
  state: 'Maharashtra',
  country: 'India',
};

const person = {
  name: 'Nikhil',
  age: 21,
  ...address,
};
console.log(person);
// {
//   name: 'Nikhil',
//   age: 21,
//   city: 'Pune',
//   state: 'Maharashtra',
//   country: 'India'
// }
```

This syntax can also be used for arrays. The order of the elements is preserved in such operations.

```js
const oneToFour = [1, 2, 3, 4];
const sixToNine = [6, 7, 8, 9];

const oneToNine = [...oneToFour, 5, ...sixToNine];
console.log(oneToNine); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## Destructuring

This provides us a way to bind variable to object properties without accessing them.

```js
const address = {
  city: 'Pune',
  state: 'Maharashtra',
  country: 'India',
};
const { city, state, country: nation } = address;
console.log(city); // Pune
console.log(state); // Maharashtra
console.log(nation); // India
```

This works on arrays also.

```js
const name = ['Billy', 'Joel'];
const [firstName, lastName] = name;
console.log(firstName); // Billy
console.log(lastName); // Joel
```

The spread syntax can be used in destructuring as well.

```js
const person = {
  name: 'Nikhil',
  age: 21,
  city: 'Pune',
  state: 'Maharashtra',
  country: 'India',
};
const { name, age, ...address } = person;
console.log(name); // Nikhil
console.log(age); // 21
console.log(address);
// {
//   city: 'Pune',
//   state: 'Maharashtra',
//   country: 'India'
// }

const languages = [
  'Python',
  'Javascript',
  'Haskell',
  'Elm',
  'Clojure',
  'Elixir',
];
const [first, second, ...rest] = languages;
console.log(first); // Python
console.log(second); // Javascript
console.log(rest); // ['Haskell', 'Elm', 'Clojure', 'Elixir']
```

## String Literal

This allows us to embed expressions in a string. Back tick are used to define a string and `${}` is used to access variables and convert them to string.

```js
const firstName = 'Charles';
const lastName = 'Hoskinson';

const greetings = `I am ${firstName} ${lastName}`
console.log(greetings) // I am Charles Hoskinson

const add = (x, y) => x + y;
const fstNumber = 5
const sndNumber = 7
const increament = `${fstNumber} + 1 = ${fstNumber + 1}`
const equation = `${fstNumber} + ${sndNumber} = ${add(fstNumber, sndNumber)}`
console.log(increament) // 5 + 1 = 6
console.log(equation) // 5 + 7 = 12 
```