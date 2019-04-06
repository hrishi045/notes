# Rust Enums

- Enumerations let you define a type by enumerating (listing) its possible values.
- The capabilities of enums differ from language to language.
- Rust's enums are similar to algebraic data types in functional languages. 

### Defining an Enum

```rust
enum IpAddrKind {
    V4,
    V6,
}
```

```rust
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

- Notice how the variants of enum are namespaced under its identifier.
- To define functions that take an `IpAddrKind`:

```rust
fn route(ip_type: IpAddrKind) { }
```

```rust
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

- We don't have a way to store the actual data yet.
- We can do that by combining it with a struct.

```rust
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```

- You can also do this using just an enum, rather than an enum inside a struct, by putting data directly into each enum variant.

```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));
let loopback = IpAddr::V6(String::from("::1"));
```

- Each variant can have different types and amounts of associated data.

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

### `Option` Enum

- Used in scenarios where a value could be something or it could be nothing. 
- This helps prevents bugs that are common in other programming languages.

```rust
let some_number = Some(5);
let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

- To access the value, there are a few methods defined on `Option<T>` that you could use:

```rust
let some_number = Some(5);
println!("{}", some_number.unwrap());
```

### `match` Control Flow Operator

- Lets you compare against a series of patterns and then execute code based on which pattern matches.
- Patterns can be literal values, variable names, wildcards, and many other things.

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => {
        	println!("Lucky Penny!");
            1
        },
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

- Matching with `Option<T>` is done like so:

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

- Matches are exhaustive, which means every possible case needs to be covered.
- If you don't want to list all possible values, you can use `_` placeholder.

```rust
let some_u8_value = 0u8;

match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),
}
```

- `()` is just the unit value, so nothing will happen in `_` case.
- `match` expression can be a bit wordy in a situation in which we care about only one of the cases.

### Control flow with `if let`

- Consider the following code which only matches one particular case, a `Some(3)`.

```rust
let some_u8_value = Some(0u8);

match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
```

- You could do this, but there's a lot of boilerplate code.

```rust
if let Some(3) = some_u8_value {
    println!("three");
}
```

- But you lose the exhaustive checking that `match` provides.
- You can also use an `else` expression with this.