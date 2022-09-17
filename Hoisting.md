# Hoisting

## var and function hoisting

We will learn what is `Hoisting` in this section.

Let's take this piece of code for an example.

```JavaScript
var x = 10;

function getName() {
    console.log("John Wick");
}

getName();
console.log(x);
```

Here the output will be.

> John Wick

> 10

Now let's shake up our code a little bit shall we ?

what will be the output of this now ?

```JavaScript
getName();

console.log(x);

var x = 10;

function getName() {
    console.log("John Wick");
}
```

Output :

> John Wick

> undefined

Now in any language this would have been an error. why? Because here we are trying to log a `var x` before its `initialization` and we are invoking a function before even defining it.

This is because of `Hoisting`.

JavaScript Hoisting refers to the process whereby the interpreter appears to move the declaration of functions, variables or classes to the top of their scope, prior to execution of the code. Hoisting allows functions to be safely used in code before they are declared.

This happens because when JavaScript starts executing code, it initilaizes values to variables as `undefined` and function as `the whole code block` (remember Execution context notes that you might have read earlier).

However, JavaScript only hoists declarations, not initializations! This means that initialization doesn't happen until the associated line of code is executed, even if the variable was originally initialized then declared, or declared and initialized in the same line.

---

## let and const hoisting

Variables declared with let and const are also hoisted but, unlike var, are not initialized with a default value. An exception will be thrown if a variable declared with let or const is read before it is initialized.

```JavaScript
console.log(num); // Throws ReferenceError exception as the variable value is uninitialized
let num = 6; // Initialization
```

> Note that it is the order in which code is executed that matters, not the order in which it is written in the source file. The code will succeed provided the line that initializes the variable is executed before any line that reads it.

---

## Class Hoisting

Classes defined using a `class declaration` are hoisted, which means that JavaScript has a reference to the class. However the class is not initialized by default, so any code that uses it before the line in which it is initialized is executed will throw a ReferenceError.

Declaration example :

```JavaScript
class Square {
  constructor(length) {
    this.length = length;
  }
}
```

An important difference between function declarations and class declarations is that while functions can be called in code that appears before they are defined, classes must be defined before they can be constructed. Code like the following will throw a ReferenceError:

```JavaScript
const p = new Rectangle(); // ReferenceError

class Rectangle {}
```

This occurs because while the class is hoisted its values are not initialized.
