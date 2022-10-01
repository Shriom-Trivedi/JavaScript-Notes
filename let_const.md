# Let & Const in JavaScript

Does variables declared with let and const gets hoisted?

Answer is YES!

let & const variables are also hoisted but they are not initialized with a value.

This certain phase when let and const variables are hoisted and are not initialized yest is called `Temporal Dead Zone`.

<!-- [let_and_const_hoisting_example]() -->

When variables are in `Temporal Dead Zone` they cannot be accessed.
That's why it throws ReferenceError: cannot access [variable name] before it's initialization.

`const` is a signal that the identifier won’t be reassigned.

`let` is a signal that the variable may be reassigned, such as a counter in a loop, or a value swap in an algorithm. It also signals that the variable will be used only in the block it’s defined in, which is not always the entire containing function.

`var` is now the weakest signal available when you define a variable in JavaScript. The variable may or may not be reassigned, and the variable may or may not be used for an entire function, or just for the purpose of a block or loop.