# Rust Structs

- Structs let you group together related data.
- They're like tuples, but the pieces of data are named.

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

```rust
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

- While building a struct, use the field init shorthand:

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

- Struct Update Syntax

```rust
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername123"),
    ..user1
};
```

### Tuple Structs

- These are structs that look similar to tuples. 
- Tuple structs have no names associated with their fields.

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0);
```

### Structures without fields

- You can define structures without any fields.
- They are called *unit-like* structs because they behave similarly to `()`, the unit type.

### Methods

- Methods can be defined within the context of structs (or enums, traits).
- The first parameter to a struct is `self`, which represents the instance of the struct the method is being called on.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let r = Rectangle { width: 10, height: 6 };
    println!("The area is {}", r.area());
}
```

- `&self` is used instead of `self` so as to not take ownership of the struct instance.
- If you wanted to mutate the struct instance, make `&mut self` as the first parameter.
- Rust has automatic referencing and dereferencing, so you could just do `object.method()` instead of `(&object).method()` or `(&mut object).method()`.
- To make methods with more parameters:

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
    
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

- Associated functions can be declared on structs, which don't take `self` as a parameter. They don't act on an instance, but rather behave like regular functions (which they are).

```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}
```

- You can also have multiple `impl` blocks.

