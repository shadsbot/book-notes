# The Rust Programming Language
_by Steve Klabnik and Carol Nichols, with contributions from the Rust Community_
[[link]](https://doc.rust-lang.org/book/title-page.html)


## Chapter 1: Getting Started
- `rustc {file}` compiles a file

- `cargo new {project}` will create a new project, whos code will live in the `{project}/src` directory

- `cargo check` will check to make sure your code will compile without issue, and is faster than `cargo build` as it is not actually creating the executable.

- `cargo run` will build the project if necessary.

## Chapter 3: Common Programming Concepts
- Variables by default are not mutable, and must be explicitly defined as such by using `mut` [[more]](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#variables-and-mutability)

- Constants must be defined with `const` instead of `let` and must be annotated.
    - Constants cannot be the result of an expression, or any other computed value. [[more]](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#differences-between-variables-and-constants)

- Shadowing a variable will allow you to change its mutability through redefinition, and allow creation of a variable with the same name of a different type. [[more]](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#shadowing)
    - Note: You cannot change the type of the same variable, even if it's mutable. Shadowing will create another variable, which shares the name, of a different type.

- In the event of Integer Overflow/Underflow, a rust program will panic. If the program is built using `--release`, it allows the over/underflow to take place as expected.

- The default floating point type is `f64`, which has greater precision than `f32`.

- `char`s in rust have unicode support. Emojis all day every day.

- A tuple is a fixed-length compound variable defined as `tup = (1, 2, 3);` [[more]](https://doc.rust-lang.org/book/ch03-02-data-types.html#the-tuple-type)
    - Can have values extracted via _destructuring_
        - `let (x, y, z) = tup;`
    - Can access values by index
        - `let one = tup.0`
    - Can contain different types
        - `let tup: (i32, f64, u8) = (1, 2.0, 3);`

- Arrays are allocated on the stack, and of fixed-length. They must be restricted to one data-type.
    ```rust
    let a = [1, 2, 3, 4, 5]; // conventional
    let a: [i32, 5] = [1, 2, 3, 4, 5]; // specified type
    let a = [3; 5]; // shortcut: [3, 3, 3, 3, 3]
    ```

> ### Common Data Types in Rust
> |Type|Name|Size|Stack|Heap|
> |---|---|---|---|---|
> |int|i|8/32/64/128|x|-|
> |uint|u|8/32/64/128|x|-|
> |float|f|8/32/64/128|x|-|
> |char|c|4|x|-|
> |boolean|bool|1|x|-|
> |string literal|str|-|x|-|
> |string|String|-|-|x|
> |tuple|tup|-|x|x|

- Functions are declared with `fn`, and the naming convention follows `snake_case`. Function definition does not have to be sequential. [[more]](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html)
    - If a line ends with a `;`, then it does not return a value, it is an expression. Lines without `;` can be treated as equivalent to `return {something}`
    - Return types can be defined by using `-> {type}` in the function signature.
- Rust follows the `if {condition}` `else if {condition}` `else` pattern.
    - `if` statements can be used in conjunction with `let` to allow a variable to be set to the result of an expression.
        - The various potential values of that conditional statement's return must be of the same type.
- Rust has three kinds of loops: `loop`, `while`, and `for`
    - `loop` will run indefinitely until told to stop via interrupt or by a `break` statement
    - `while` will run as long as a condition is met
    - `for` will run until a condition is met

## Chapter 4: Understanding Ownership [[more, important]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html)
Most programming languages either leave you to figure out memory management on your own, or they have a gabage collector that will manage memory for you. Rust's approach is to manage memory at compile by using the "ownership" of objects.

>### Reveiw: Stack and Heap [[more]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#the-stack-and-the-heap)
>- Stack operates in LIFO order
>- Stack data must have a known fixed size
>- Heap is whatever memory is available of the requested size. Usually in form of a pointer.
>    - Pointer can be stored on the stack
>- Working on the stack is faster than working on the heap

`<!>` Each value in Rust has a variable that's called its `owner`, and can only have one owner at a time. When an owner goes out of scope, so does that value. [[more]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#ownership-rules)

### Memory and Allocation
Once a variable goes out of scope, a `drop` function is called for that datatype which deallocates it.
When a value is copied, as below:
```rust
let x = 5;
let y = x;
```
... x is being assigned to 5, and y is being assigned to a copy of x, so y also equals 5. **This is not the case with strings and other heap data-types.** Instead, the pointer to the location of the string on the heap is copied. Rust will also invalidate the first string so it cannot be used to prevent double deallocation. This is essentially a shallow copy, but because of that invalidation, it's actually a *move*. For a full deep copy, use `.clone()`. [[more, important]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#ways-variables-and-data-interact-move)

### References
- Ownership is not passed for heap objects if you pass a reference instead of the object itself
    - These references are immutable by default
- You may only have one mutable reference in scope at any given time
- You cannot create a mutable reference if you have already created an immutable reference
- Scope ends when a var is last used?

### Slices
- Slices do not have ownership
- Denoted as `&s[start..end]` 
    - Can have `start` or `end` ommitted
- `Strings` can be sliced to be valid input for `&str`



## Chapter 1: Getting Started
- `rustc {file}` compiles a file

- `cargo new {project}` will create a new project, whos code will live in the `{project}/src` directory

- `cargo check` will check to make sure your code will compile without issue, and is faster than `cargo build` as it is not actually creating the executable.

- `cargo run` will build the project if necessary.

## Chapter 3: Common Programming Concepts
- Variables by default are not mutable, and must be explicitly defined as such by using `mut` [[more]](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#variables-and-mutability)

- Constants must be defined with `const` instead of `let` and must be annotated.
    - Constants cannot be the result of an expression, or any other computed value. [[more]](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#differences-between-variables-and-constants)

- Shadowing a variable will allow you to change its mutability through redefinition, and allow creation of a variable with the same name of a different type. [[more]](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#shadowing)
    - Note: You cannot change the type of the same variable, even if it's mutable. Shadowing will create another variable, which shares the name, of a different type.

- In the event of Integer Overflow/Underflow, a rust program will panic. If the program is built using `--release`, it allows the over/underflow to take place as expected.

- The default floating point type is `f64`, which has greater precision than `f32`.

- `char`s in rust have unicode support. Emojis all day every day.

- A tuple is a fixed-length compound variable defined as `tup = (1, 2, 3);` [[more]](https://doc.rust-lang.org/book/ch03-02-data-types.html#the-tuple-type)
    - Can have values extracted via _destructuring_
        - `let (x, y, z) = tup;`
    - Can access values by index
        - `let one = tup.0`
    - Can contain different types
        - `let tup: (i32, f64, u8) = (1, 2.0, 3);`

- Arrays are allocated on the stack, and of fixed-length. They must be restricted to one data-type.
    ```rust
    let a = [1, 2, 3, 4, 5]; // conventional
    let a: [i32, 5] = [1, 2, 3, 4, 5]; // specified type
    let a = [3; 5]; // shortcut: [3, 3, 3, 3, 3]
    ```

> ### Common Data Types in Rust
> |Type|Name|Size|Stack|Heap|
> |---|---|---|---|---|
> |int|i|8/32/64/128|x|-|
> |uint|u|8/32/64/128|x|-|
> |float|f|8/32/64/128|x|-|
> |char|c|4|x|-|
> |boolean|bool|1|x|-|
> |string literal|str|-|x|-|
> |string|String|-|-|x|
> |tuple|tup|-|x|x|

- Functions are declared with `fn`, and the naming convention follows `snake_case`. Function definition does not have to be sequential. [[more]](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html)
    - If a line ends with a `;`, then it does not return a value, it is an expression. Lines without `;` can be treated as equivalent to `return {something}`
    - Return types can be defined by using `-> {type}` in the function signature.
- Rust follows the `if {condition}` `else if {condition}` `else` pattern.
    - `if` statements can be used in conjunction with `let` to allow a variable to be set to the result of an expression.
        - The various potential values of that conditional statement's return must be of the same type.
- Rust has three kinds of loops: `loop`, `while`, and `for`
    - `loop` will run indefinitely until told to stop via interrupt or by a `break` statement
    - `while` will run as long as a condition is met
    - `for` will run until a condition is met

## Chapter 4: Understanding Ownership [[more, important]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html)
Most programming languages either leave you to figure out memory management on your own, or they have a gabage collector that will manage memory for you. Rust's approach is to manage memory at compile by using the "ownership" of objects.

>### Reveiw: Stack and Heap [[more]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#the-stack-and-the-heap)
>- Stack operates in LIFO order
>- Stack data must have a known fixed size
>- Heap is whatever memory is available of the requested size. Usually in form of a pointer.
>    - Pointer can be stored on the stack
>- Working on the stack is faster than working on the heap

`<!>` Each value in Rust has a variable that's called its `owner`, and can only have one owner at a time. When an owner goes out of scope, so does that value. [[more]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#ownership-rules)

### Memory and Allocation
Once a variable goes out of scope, a `drop` function is called for that datatype which deallocates it.
When a value is copied, as below:
```rust
let x = 5;
let y = x;
```
... x is being assigned to 5, and y is being assigned to a copy of x, so y also equals 5. **This is not the case with strings and other heap data-types.** Instead, the pointer to the location of the string on the heap is copied. Rust will also invalidate the first string so it cannot be used to prevent double deallocation. This is essentially a shallow copy, but because of that invalidation, it's actually a *move*. For a full deep copy, use `.clone()`. [[more, important]](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#ways-variables-and-data-interact-move)

### References
- Ownership is not passed for heap objects if you pass a reference instead of the object itself
    - These references are immutable by default
- You may only have one mutable reference in scope at any given time
- You cannot create a mutable reference if you have already created an immutable reference
- Scope ends when a var is last used?

### Slices
- Slices do not have ownership
- Denoted as `&s[start..end]` 
    - Can have `start` or `end` ommitted
- `Strings` can be sliced to be valid input for `&str`
