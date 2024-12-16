# Rust language content dump for talks

## Inspirations (that nobody cares about)

- SML, OCaml: algebraic data types, pattern matching, type inference, semicolon statement separation
- C++: references, RAII, smart pointers, move semantics, monomorphization, memory model
- ML Kit, Cyclone: region based memory management
- Haskell (GHC): typeclasses, type families
- Newsqueak, Alef, Limbo: channels, concurrency
- Erlang: message passing, thread failure, linked thread failure, lightweight concurrency
- Swift: optional bindings
- Scheme: hygienic macros
- C#: attributes
- Ruby: closure syntax, block syntax
- NIL, Hermes: typestate
- Unicode Annex #31: identifier and pattern syntax

##  RAII example

```cpp
std::mutex m;
 
void bad() 
{
    m.lock();                    // acquire the mutex
    f();                         // if f() throws an exception, the mutex is never released
    if(!everything_ok()) return; // early return, the mutex is never released
    m.unlock();                  // if bad() reaches this statement, the mutex is released
}
 
void good()
{
    std::lock_guard<std::mutex> lk(m); // RAII class: mutex acquisition is initialization
    f();                               // if f() throws an exception, the mutex is released
    if(!everything_ok()) return;       // early return, the mutex is released
}
```

## Immutability

from [doc](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html)

Vars immutable by default. Compiler warning if variable not mutated after declaring `mut`. Error if immutable variable reassigned.

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6; // error
    println!("The value of x is: {x}");
}
```

Working code:

```rust
fn main() {
    let mut x = 5; // <-- added mut
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

## Shadowing

refer [doc](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#shadowing)

## Ownership

Passing ownership from one function to another

```rust
let s1 = String::from("hello");
let s2 = s1; // s1 moved; can't be used again :(
let s3 = s2.clone(); // s2 copied; both can be used independently
```


- Move
- Copy

(Move and copy are one of the many traits)

## Passing ownership

String type can take arbitrary time to Copy. Hence default action is to pass its ownership (i.e. `Move` it). On the other hand, integers are easy to copy and take deterministic time. Hence they are cloned by default (i.e. `Copy`)

```rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it's okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

## Function can return ownership

If function returns a value, the calling function can capture return data and gets ownership.



## References
refer [doc](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)

Use `&` to pass a reference

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```


But using `&` doesn't mean you can edit the variable 

```rust
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world"); // <-- Error
}
```

## Mutable References to other functions

refer [doc](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html#mutable-references)

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```


## Mutable References within scope

```rust
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    let r3 = &mut s; // BIG PROBLEM

    println!("{}, {}, and {}", r1, r2, r3);

```

## Dangling references

Situation where a pointer is pointing to data which has been dropped

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
} // After function exits, the string data is dropped; 
	// and ownership of reference s is given back to main
```

This code won't compile.


## Error handling philosophy

There is only ONE error which halts your program / crashes it. That is `panic!()`. No other error shall arbitrarily halt your program. If a program panics, then run the program with env var `RUST_BACKTRACE=1` to print backtrack from where error occured.

`panic!()` is called only when the error is NOT recoverable. 
- trying to open a non existent file without telling Rust what to do if file doesn't exist
- trying to send network packet when there is no network connection and not telling Rust what to do when no network

Most of the panics occur because an external requirement is not satisfied. (like existence of data on filesystem, network connectivity etc). 

**But the program will still NOT panic arbitrarily.** If you access a file that doesn't exist, Rust won't crash the program. In the code itself YOU, the programmer, has to explicitly define what should happen if file doesn't exist. Handle the error or crash program.

This approach to error handling makes the code verbose. There is no *default behaviour*. There is only *explicit behaviour* which YOU have defined. This verbosity comes at a benefit that if your program is crashing, then its because you ASKED it to crash. Program doesn't crash because "something went wrong". 

## Result type

Refer [Recoverable Errors with Result](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html)

`Result`

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```


Functions don't return the value you expect. It returns a `Result`.

```rust
use std::fs::File;

fn main() {
    let file_handler_result = File::open("hello.txt");
		// file_handler_result is of type Result<std::fs::File, std::io::Error>
}
```

Now YOU decide what to do with this result.

Handling it by panicking

```rust
use std::fs::File;

fn main() {
    let file_handler_result = File::open("hello.txt");

    let file_handler = match file_handler_result {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

Similar to

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap();
}
```

Or panic with error message

```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
}
```

In all these cases, its YOU, who asked the program to crash. Of course, this isn't any better than C++ yet.

### Matching on various errors

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let greeting_file_result = File::open("hello.txt");

    let greeting_file = match greeting_file_result {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening the file: {:?}", other_error);
            }
        },
    };
}
```


Same thing with closures

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```


## Smart pointer - Box

```rust
fn main() {
    let b = Box::new(5);
    println!("b = {}", b);
}
```

When `b` goes out of scope, the value stored on heap is also dropped automatically.

further talk
- why making linked list is a pain
	- Cons type

## Traits

Opposite to object orientedness

Traits can be derived

- `Debug` for detailed  printing of a variable
- `PartialEq` for comparing values with `==` or `!=`
- `Clone`
