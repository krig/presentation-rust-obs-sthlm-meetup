<img src="img/rust_logo_white.svg" height="300px">

> Rust is a systems programming language that runs blazingly fast, prevents segfaults, and guarantees thread safety.

<small>&mdash; <a href="http://rust-lang.org">rust-lang.org</a></small>

---

<img src="img/tumbleweed-icon.svg">

### openSUSE Tumbleweed

---

<img src="img/opensuse-regular-release-icon.svg">

### openSUSE Leap

---

<img src="img/butwhy.jpg" >

--

# Zero Overhead

--

# Memory Safety

--

# Thread Safety (*)

*: No data races

--

# High Performance

--

# Interoperability

--

# Firefox

---

<img src="img/butwhy2.jpg" >

--

# Why package for distributions?

--

# Control dependencies

--

# Security updates

--

# Stability

--

# openQA

--

<iframe width="560" height="315" src="https://www.youtube.com/embed/SYRlTISvjww" frameborder="0" allowfullscreen></iframe>

---

### hello.rs

```rust
fn main() {
    println!("Hello World!");
}
```

---

### compile and run

```
$ rustc hello.rs
$ ./hello
Hello World!
```

---


```rust
use std::sync::{ Arc, Mutex };
use std::sync::mpsc::channel;
use std::collections::HashMap;
fn main() {
    let (c_tx, c_rx) = channel();
    let map = Arc::new(Mutex::new(HashMap::new()));
    let tmap = map.clone();
    std::thread::spawn(move || {
        if let Ok(mut m) = tmap.lock() {
            m.insert("thread", "hello");
        }
        c_tx.send(true).unwrap();
    });
    if let Ok(mut m) = map.lock() {
        m.insert("main", "hello");
    }
    c_rx.recv().unwrap();
    for (k, v) in map.lock().unwrap().iter() {
        println!("{} says {}", k, v);
    }
}
```

---

scoped allocation
```rust
fn create_box() {
    let _box1 = Box::new(3i32);
}

fn main() {
    let _box2 = Box::new(5i32);

    {
        let _box3 = Box::new(4i32);
    }

    for _ in 0u32..1_000 {
        create_box();
    }
}
```

---

ownership + moving
```rust
fn destroy_box(c: Box<i32>) {
    println!("Destroying a box that contains {}", c);
}

fn main() {
    let x = 5u32;
    let y = x;
    println!("x is {}, and y is {}", x, y);

    let a = Box::new(5i32);
    println!("a contains: {}", a);

    let b = a;
    //println!("a contains: {}", a);
    // TODO ^ Try uncommenting this line

    destroy_box(b);
    //println!("b contains: {}", b);
    // TODO ^ Try uncommenting this line
}
```

---

ownership + borrowing
```rust
struct Point { x: i32, y: i32, z: i32 }

fn main() {
    let mut point = Point { x: 0, y: 0, z: 0 };

    { let borrowed_point = &point;
      let another_borrow = &point;
      println!("Point has coordinates: ({}, {}, {})",
               borrowed_point.x, another_borrow.y, point.z);
    }

    { let mutable_borrow = &mut point;
      mutable_borrow.x = 5;
      mutable_borrow.y = 2;
      mutable_borrow.z = 1;
    }

    println!("Point now has coordinates: ({}, {}, {})",
             point.x, point.y, point.z);
}
```

---

traits
```rust
trait Animal {
    fn new(name: &'static str) -> Self;

    fn name(&self) -> &'static str;
    fn noise(&self) -> &'static str;

    fn talk(&self) {
        println!("{} says {}", self.name(), self.noise());
    }
}
```

---

```rust
struct Sheep { naked: bool, name: &'static str }

impl Sheep {
    fn is_naked(&self) -> bool {
        self.naked
    }

    fn shear(&mut self) {
        if self.is_naked() {
            println!("{} is already naked...", self.name());
        } else {
            println!("{} gets a haircut!", self.name);

            self.naked = true;
        }
    }
}
```

---

```rust
impl Animal for Sheep {
    fn new(name: &'static str) -> Sheep {
        Sheep { name: name, naked: false }
    }

    fn name(&self) -> &'static str {
        self.name
    }

    fn noise(&self) -> &'static str {
        if self.is_naked() {
            "baaaaah?"
        } else {
            "baaaaah!"
        }
    }

    fn talk(&self) {
        println!("{} pauses briefly... {}", self.name, self.noise());
    }
}
```

---

```rust
fn main() {
    let mut dolly: Sheep = Animal::new("Dolly");
    dolly.talk();
    dolly.shear();
    dolly.talk();
}
```

---

### macros

```rust
macro_rules! println {
    ($fmt:expr) => (print!(concat!($fmt, "\n")));
    ($fmt:expr, $($arg:tt)*) => (print!(concat!($fmt, "\n"), $($arg)*));
}
```

---

# My Favorite Things

* `if let`
* `match`
* `traits`
* `cargo`

---

<img src="img/cargo.png">

# cargo

---

```bash
$ cargo new hello --bin
$ cd hello
```

---

`Cargo.toml`
```toml
[package]

name = "hello"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
```

---

```bash
$ cargo build
  Compiling hello v0.1.0 (file:///home/you/hello)
```

---

# crates.io

Package hub (like CPAN, PyPI, ...)

---

```toml
[dependencies]

rand="0.3.0"
```

---

```bash
$ cargo build
    Updating registry
      `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.8
 Downloading libc v0.1.6
   Compiling libc v0.1.6
   Compiling rand v0.3.8
   Compiling hello v0.1.0 (file:///home/you/hello)
```

---

# Status

#### `devel:languages:rust/rustc`
#### `devel:languages:rust/cargo-bootstrap`

<strike>

**Goal:** rustc 1.10 in openSUSE:Tumbleweed

</strike>

**New goal:** rustc 1.12 in openSUSE:Tumbleweed :(

---

<img src="img/RPM_Logo.svg">

---

<img src="img/obs-logo.png" style="background: #eeeeee; padding: 32px; border-radius: 8px;">

---

# BOOTSTRAPPING

---

# rustc

---

# cargo

---

# crates

---

# `cargo-vendor`

<small>

`https://github.com/alexcrichton/cargo-vendor`

</small>

---

## Issues

* 6-week release cycle
* Slow compilation
* Static linking
* No stable ABI
* Can't use packaged LLVM
