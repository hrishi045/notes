---
title: Rust Ownership
---

# Rust Ownership

From: [The Rust Book - Ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)

### Background

- Every bit of memory allocated in the heap needs to be deallocated. 
- In C, the deallocation is usually done in the calling scope, using `free()`.
- In C++, RAII (Resource Acquisition Is Initialization) patterns are commonly used.
- Languages with garbage collection take care of the deallocation (and allocation).
- Rust manages memory differently.

### Rust's Concept of Ownership

- Rust programs are not garbage collected.
- It uses a system of *ownership.*
- *Ownership* absolves the programmer from having to think about the heap, and manually allocating and deallocating memory.

### Ownership Rules

- Each value in Rust has a variable that's called its *owner*.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.

### The `String` type

- Rust includes a `String` type that, in contrast with the string literals, is allocated on the heap.
- A `String` can be mutated.
```rust
let mut s = String::from("Hello");
s.push_str(" World");
println!("{}", s);
```
- String literals are hard-coded into the executable, and must be known at compile-time. For unknown strings, the `String` type must be used.

### Memory and Allocation

- In Rust, the memory is automatically deallocated after the variable that owns it goes out of scope.

### Variables and Allocation

- These two pieces of code look similar, but they don't function the same.

- Here, a *copy* is performed. That is, the data stored in `x` is copied into `y`.

```rust
let x = 5;
let y = x;
```

- In this piece of code, the ownership is moved from `a` to `b`. 

```rust
let a = String::from("Hello");
let b = a;
```

- You can't use `a` anymore, because the reference is invalidated. 
- There is, however, a way to clone the data, and not just move the reference.

```rust
let m = String::from("Hello");
let n = m.clone();

println("s1 = {}, s2 = {}", s1, s2);
```

- In the first example, `x` is not invalidated because `5`, the value it owns, is stored on the stack. So, the ownership is not moved, but the value is copied.
- Rust has a `Copy` trait. And a few types are `Copy`:

| Type of Type         | Types of Type                                                |
| -------------------- | ------------------------------------------------------------ |
| Integer Types        | `i8, u8, i16, u16, i32, u32, i64, u64, i128, u128, isize, usize ` |
| Floating-point Types | `f32, f64`                                                   |
| Boolean              | `bool`                                                       |
| Character            | `char`                                                       |
| Compound types       | Tuples: `(i32, f64)`<br>Arrays: `[i32; 5]`                   |

### Ownership and Functions

- Semantics for passing a value to a function are similar to those for assigning a value to a variable.

```rust
fn take_ownership(x: String) {
    println!("{}", x);
}

fn copy_value(x: i32) {
    println!("{}", x);
}

fn main() {
    let x = 32;
    let y = String::from("Hello");
    
    take_ownership(y);
    copy_value(x);
}
```
- The ownership of the `String` is moved and the variable `y` cannot be used anymore.
- You can also transfer ownership from a function to the calling scope by returning values.

```rust
fn gives_ownership() -> String {
    String::from("From function")
}

fn takes_and_gives_ownership(x: String) -> String {
    x
}

fn main() {
    let x = String::from("Hello");
    
    let x = takes_and_gives_ownership(x);
    println!("{}", x);
    
    let y = gives_ownership();
    println!("{}", y);
}
```

### References and Borrowing

- The `takes_and_gives_ownership()` is great, it doesn't steal the ownership, but you'd have to return the `String`. 
- To avoid having to return the ownership, *references* can be used.
- The values are *borrowed* when a reference is passed.

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
}

fn main() {
    let s = String::from("Hello");
    let len = calculate_length(&s);
    println!("The length of '{}' is {}.", s, len);
}
```

- The code would work because only a reference is passed.
- `&s` creates a reference that *refers* to the value of `s`, but does not own it.
- So, the value it points to will not be dropped when the reference goes out of scope.
- But the value cannot be mutated.
- To make mutable references, use the `&mut` keyword.

```rust
fn change(x: &mut String) {
    x.push_str(" World");
}

fn main() {
    let mut s = String::from("Hello");
    change(&mut s);
    println!("{}", s);
}
```

- There can only be one mutable reference to a particular piece of data in a particular scope.
- You also can't have a mutable reference while you have an immutable one.
- Dangling references are prevented with a compile-time error

### Slices

- *Slices* don't have ownership.
- They let you reference a contiguous sequence of elements.
- A *string slice* is part of a `String`.

```rust
let s = String::from("hello world");
let hello = &s[0..5];
let world = &s[6..10];
```

- You can also do:

```rust
let hello = &s[0..=4];
let world = &s[6..=9];
```

- Here's a function that demonstrated the use of slices. It returns the first word of a `String` as a slice. 

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();
    
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[..i];
        }
    }
    
    &s[..]
}

fn main() {
    let s = String::from("Hello World");
    let fw = first_word(&s);
    println!("{}", fw);
}
```

- Slices as parameters: we can also write the above function like so:

```rust
fn first_word(s: &str) -> &str {
```

- There are also other types of slices. Like array slices. 

```rust
let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];
```

