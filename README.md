## Note
This crate has been republished because of popular demand to publish the fixed fork as a crate.
However, I can't make any guarantees about the safety of this crate, and I won't necessarilly be able to actively maintain it.


safer_owning_ref
==============
A library for creating references that carry their owner with them.

This can sometimes be useful because Rust borrowing rules normally prevent
moving a type that has been borrowed from. For example, this kind of code gets rejected:

```rust
fn return_owned_and_referenced<'a>() -> (Vec<u8>, &'a [u8]) {
    let v = vec![1, 2, 3, 4];
    let s = &v[1..3];
    (v, s)
}
```

This library enables this safe usage by keeping the owner and the reference
bundled together in a wrapper type that ensure that lifetime constraint:

```rust
fn return_owned_and_referenced() -> OwningRef<Vec<u8>, [u8]> {
    let v = vec![1, 2, 3, 4];
    let or = OwningRef::new(v);
    let or = or.map(|v| &v[1..3]);
    or
}
```

## Getting Started

To get started, add the following to `Cargo.toml`.

```toml
safer_owning_ref = "0.5.0"
```
(Note: The package's name is `safer_owning_ref`, but the crate name is still `owning_ref`).

...and see the [docs](https://docs.rs/safer_owning_ref/latest/) for how to use it.


## Example

```rust
extern crate owning_ref;
use owning_ref::BoxRef;

fn main() {
    // Create an array owned by a Box.
    let arr = Box::new([1, 2, 3, 4]) as Box<[i32]>;

    // Transfer into a BoxRef.
    let arr: BoxRef<[i32]> = BoxRef::new(arr);
    assert_eq!(&*arr, &[1, 2, 3, 4]);

    // We can slice the array without losing ownership or changing type.
    let arr: BoxRef<[i32]> = arr.map(|arr| &arr[1..3]);
    assert_eq!(&*arr, &[2, 3]);

    // Also works for Arc, Rc, String and Vec!
}
```
