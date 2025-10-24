# Role of Rust wayland_scanner

The task of converting the Wayland XML protocol definition into Rust data types, like structs and traits, for use in a crate like **Smithay** is handled by the **`wayland-scanner`** utility, specifically the **Rust version** of it, often used via the **`wayland-scanner` Rust crate** and its procedural macros.

The key points are:

1.  **Original `wayland-scanner` (C-based):** This tool, which you used, reads the Wayland XML protocol file (e.g., `wayland.xml` or custom protocols) and generates C header files (like your `wayland-protocol.h`) and C source files.
2.  **Rust `wayland-scanner` Crate:** The Smithay project is part of the broader `wayland-rs` ecosystem. They developed a separate Rust library and procedural macros named **`wayland-scanner`** (which is a Rust crate, not the original C program) that takes the same Wayland XML protocol files and directly generates **Rust code** (structs, enums, traits, and other necessary bindings) for both the client and server sides.
3.  **Core Libraries:** The generated Rust code relies on the underlying Rust crates like **`wayland-client`** (for clients) and **`wayland-server`** (for compositors like Smithay) to provide the necessary runtime logic and low-level Wayland communication handling.
4.  **Pre-generated Protocols:** For standard protocols (like the core Wayland protocol and official extensions), Smithay typically doesn't run the scanner itself; instead, it relies on the **`wayland-protocols`** crate, which already contains the Rust bindings generated from the standard XML files using the `wayland-scanner` Rust crate.

In short, the library that takes care of the job of converting the protocol XML into Rust code is the **`wayland-scanner`** Rust crate, and the actual Rust code that Smithay uses is provided by the **`wayland-protocols`** crate for standard protocols, or is generated via the Rust `wayland-scanner` in its build process for custom or external protocols. 

## See also

[https://github.com/Smithay/wayland-rs/tree/master/wayland-scanner](https://github.com/Smithay/wayland-rs/tree/master/wayland-scanner)
