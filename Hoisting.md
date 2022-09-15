# Hoisting

Hello there !

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