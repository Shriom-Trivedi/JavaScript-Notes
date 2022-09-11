# JavaScript Notes
Everythings in JavaScript happens inside an `Execution Context`.

This Execution Context consists of two blocks :
1. Memory (Variable Environment) - Its is where functions and variables are stored as a `key:value` pair.
2. Code (Thread of Execution) - This code component (Thread of Execution) is the place where JS code is executed.

## Thread of Execution
Javascript is a `synchronus single-threaded language`. That means JS can execute only a single command at a time and in a certain order.

### Wait? if Javascript is a synchronus single-threaded language then why does `A` in `AJAX` stands for `Asynchronus`.
### But more on that later!
***
## Execution of code
Let's take an example.

```JavaScript
var n=2;
function square(num) {
    var ans = num * num;
    return ans;
}
var square2 = square(n);
var square4 = square(4);
```

Let's divide this code block into memory and code component.

Execution Context is created in 2 phases
* Memory Creation Phase
* Code Execution Phase

#### First phase of Memory Creation
When JS encounters line 1, it will allocate memory to n with `undefined`,
and for the next line it will store `square()` as the whole code.

|Memory||
|------|----|
|n : `undefined`||
|square : { `...code` }||
|square2 : `undefined`||
|square4 : `undefined`||

---

### Second phase of Code Execution
After memory memory creation we will move onto next phase i.e. `Code Execution`.

The code execution will take place line by line. So when it will encounter line 6 it will invoke square(). When square() is invoked it will create memory for `num` and `ans`.

|Memory||
|------|----|
|num : `undefined`||
|ans : `undefined`|| 

After invoking of function `num` will be assigned to value 2, ans will be assigned the value of `num*num` i.e. 4 in our case.

Memory||
|------|----|
|num : `2`||
|ans : `4`|| 

So after execution the `return` keyword will tell the function to return the control back to the place where the function was invoked. Hence the value of `square2` variable will be 4.

|Memory||
|------|----|
|n : `2`||
|square : { `...code` }||
|square2 : `4`||
|square4 : `undefined`||

As soon as soon as we return the `ans` the memory block of code execution block for `square2` will be deleted.

Again in the very next line we encounter another function call. This time we will be passing 4. So again initially `num` and `ans` both will be undefined.

|Memory||
|------|----|
|num : `undefined`||
|ans : `undefined`|| 

In code execution `num` will be replaced by 4, and `ans` will be replaced by 16.

|Memory||
|------|----|
|num : `4`||
|ans : `16`|| 

So after execution the `return` keyword will tell the function to return the control back to the place where the function was invoked. Hence the value of `square4` variable will be 16.

|Memory||
|------|----|
|n : `2`||
|square : { `...code` }||
|square2 : `4`||
|square4 : `16`||

As soon as soon as we return the `ans` the memory block of code execution block for `square4` will be deleted.

So after completion of the program the whole `code exection block` will be `deleted`. It goes off!

All this magic happens inside something called a `Call Stack`.

---

## Call Stack

### What is a call stack ?

A `call stack` is a mechanism for an interpreter (like the JavaScript interpreter in a web browser) to keep track of its place in a script that calls multiple functions — what function is currently being run and what functions are called from within that function, etc.

All this `Global Execution Context` is pushed inside this call stack.

When a script calls a function, the interpreter adds it to the call stack and then starts carrying out the function.

Any functions that are called by that function are added to the call stack further up, and run where their calls are reached.

When the current function is finished, the interpreter takes it off the stack and resumes execution where it left off in the last code listing.
If the stack takes up more space than it had assigned to it, it results in a "stack overflow" error.

### `Call Stack` maintains the order of execution of execution contexts.