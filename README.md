[![Build Status](https://github.com/emoon/cfixed-string/workflows/Rust/badge.svg)](https://github.com/emoon/cfixed-string/actions?workflow=Rust)
[![Crates.io](https://img.shields.io/crates/v/cfixed-string.svg)](https://crates.io/crates/cfixed-string)
[![Documentation](https://docs.rs/cfixed-string/badge.svg)](https://docs.rs/cfixed-string)

cfixed-string is used for passing Rust string to C with potentially not needing to do a heap allocation.

A problem with using the standard library `CString` is that it will always allocate memory on the heap even if the string you are trying to use is very short. This can cause performance issues and potentially adding to memory fragmentation depending on your system.

`CFixedString` will instead have a 512 byte buffer on the stack that can then be used when calling the FFI function. This allows strings that are less than 512 characters (including zero termination) to be on the stack instead of the heap which removes the need for memory allocation and free. In case the string is larger it will fallback to `CString` from the standard library.

Usage
-----

```toml
# Cargo.toml
[dependencies]
cfixed-string = "1.0"
```

Example
-------

```rust
use cfixed_string::CFixedString;

fn main() {
	// Create a string that will be stored on the stack
	let ffi_str = CFixedString::from_str("test");
	// And pass it to the FFI function
	ffi_func(ffi_str.as_ptr());

	// It's also possible to format a string directly on the stack if it fits using the format_c macro
	let fmt_str = format_c!("hello {}", 123);
	// And pass it to the FFI function
	ffi_func(ffi_str.as_ptr());
}
```
