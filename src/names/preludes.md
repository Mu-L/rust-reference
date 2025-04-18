r[names.preludes]
# Preludes

r[names.preludes.intro]
A *prelude* is a collection of names that are automatically brought into scope
of every module in a crate.

These prelude names are not part of the module itself: they are implicitly
queried during [name resolution]. For example, even though something like
[`Box`] is in scope in every module, you cannot refer to it as `self::Box`
because it is not a member of the current module.

r[names.preludes.kinds]
There are several different preludes:

- [Standard library prelude]
- [Extern prelude]
- [Language prelude]
- [`macro_use` prelude]
- [Tool prelude]

r[names.preludes.std]
## Standard library prelude

r[names.preludes.std.intro]
Each crate has a standard library prelude, which consists of the names from a single standard library module.

r[names.preludes.std.module]
The module used depends on the crate's edition, and on whether the [`no_std` attribute] is applied to the crate:

Edition | `no_std` not applied        | `no_std` applied
--------| --------------------------- | ----------------------------
2015    | [`std::prelude::rust_2015`] | [`core::prelude::rust_2015`]
2018    | [`std::prelude::rust_2018`] | [`core::prelude::rust_2018`]
2021    | [`std::prelude::rust_2021`] | [`core::prelude::rust_2021`]
2024    | [`std::prelude::rust_2024`] | [`core::prelude::rust_2024`]

> [!NOTE]
> [`std::prelude::rust_2015`] and [`std::prelude::rust_2018`] have the same contents as [`std::prelude::v1`].
>
> [`core::prelude::rust_2015`] and [`core::prelude::rust_2018`] have the same contents as [`core::prelude::v1`].

r[names.preludes.extern]
## Extern prelude

r[names.preludes.extern.intro]
External crates imported with [`extern crate`] in the root module or provided
to the compiler (as with the `--extern` flag with `rustc`) are added to the
*extern prelude*. If imported with an alias such as `extern crate orig_name as
new_name`, then the symbol `new_name` is instead added to the prelude.

r[names.preludes.extern.core]
The [`core`] crate is always added to the extern prelude.

r[names.preludes.extern.std]
The [`std`] crate is added as long as the [`no_std` attribute] is not specified in the crate root.

r[names.preludes.extern.edition2018]
> [!EDITION-2018]
> In the 2015 edition, crates in the extern prelude cannot be referenced via [use declarations], so it is generally standard practice to include `extern crate` declarations to bring them into scope.
>
> Beginning in the 2018 edition, [use declarations] can reference crates in the extern prelude, so it is considered unidiomatic to use `extern crate`.

> [!NOTE]
> Additional crates that ship with `rustc`, such as [`alloc`], and [`test`](mod@test), are not automatically included with the `--extern` flag when using Cargo. They must be brought into scope with an `extern crate` declaration, even in the 2018 edition.
>
> ```rust
> extern crate alloc;
> use alloc::rc::Rc;
> ```
>
> Cargo does bring in `proc_macro` to the extern prelude for proc-macro crates only.

<!--
See https://github.com/rust-lang/rust/issues/57288 for more about the
alloc/test limitation.
-->

r[names.preludes.extern.no_std]
### The `no_std` attribute

r[names.preludes.extern.no_std.intro]
By default, the standard library is automatically included in the crate root
module. The [`std`] crate is added to the root, along with an implicit
[`macro_use` attribute] pulling in all macros exported from `std` into the
[`macro_use` prelude]. Both [`core`] and [`std`] are added to the [extern
prelude].

r[names.preludes.extern.no_std.allowed-positions]
The *`no_std` [attribute]* may be applied at the crate level to prevent the
[`std`] crate from being automatically added into scope.

It does three things:

r[names.preludes.extern.no_std.extern]
* Prevents `std` from being added to the [extern prelude](#extern-prelude).
r[names.preludes.extern.no_std.module]
* Affects which module is used to make up the [standard library prelude] (as described above).
r[names.preludes.extern.no_std.core]
* Injects the [`core`] crate into the crate root instead of [`std`], and pulls
  in all macros exported from `core` in the [`macro_use` prelude].

> [!NOTE]
> Using the core prelude over the standard prelude is useful when either the crate is targeting a platform that does not support the standard library or is purposefully not using the capabilities of the standard library. Those capabilities are mainly dynamic memory allocation (e.g. `Box` and `Vec`) and file and network capabilities (e.g. `std::fs` and `std::io`).

> [!WARNING]
> Using `no_std` does not prevent the standard library from being linked in. It is still valid to put `extern crate std;` into the crate and dependencies can also link it in.

r[names.preludes.lang]
## Language prelude

r[names.preludes.lang.intro]
The language prelude includes names of types and attributes that are built-in
to the language. The language prelude is always in scope.

r[names.preludes.lang.entities]
It includes the following:

* [Type namespace]
    * [Boolean type] --- `bool`
    * [Textual types] --- `char` and `str`
    * [Integer types] --- `i8`, `i16`, `i32`, `i64`, `i128`, `u8`, `u16`, `u32`, `u64`, `u128`
    * [Machine-dependent integer types] --- `usize` and `isize`
    * [floating-point types] --- `f32` and `f64`
* [Macro namespace]
    * [Built-in attributes]

r[names.preludes.macro_use]
## `macro_use` prelude

r[names.preludes.macro_use.intro]
The `macro_use` prelude includes macros from external crates that were
imported by the [`macro_use` attribute] applied to an [`extern crate`].

r[names.preludes.tool]
## Tool prelude

r[names.preludes.tool.intro]
The tool prelude includes tool names for external tools in the [type
namespace]. See the [tool attributes] section for more details.

r[names.preludes.no_implicit_prelude]
## The `no_implicit_prelude` attribute

r[names.preludes.no_implicit_prelude.intro]
The *`no_implicit_prelude` [attribute]* may be applied at the crate level or
on a module to indicate that it should not automatically bring the [standard
library prelude], [extern prelude], or [tool prelude] into scope for that
module or any of its descendants.

r[names.preludes.no_implicit_prelude.lang]
This attribute does not affect the [language prelude].

r[names.preludes.no_implicit_prelude.edition2018]
> [!EDITION-2018]
> In the 2015 edition, the `no_implicit_prelude` attribute does not affect the [`macro_use` prelude], and all macros exported from the standard library are still included in the `macro_use` prelude. Starting in the 2018 edition, it will remove the `macro_use` prelude.

[`extern crate`]: ../items/extern-crates.md
[`macro_use` attribute]: ../macros-by-example.md#the-macro_use-attribute
[`macro_use` prelude]: #macro_use-prelude
[`no_std` attribute]: #the-no_std-attribute
[`no_std` attribute]: #the-no_std-attribute
[attribute]: ../attributes.md
[Boolean type]: ../types/boolean.md
[Built-in attributes]: ../attributes.md#built-in-attributes-index
[extern prelude]: #extern-prelude
[floating-point types]: ../types/numeric.md#floating-point-types
[Integer types]: ../types/numeric.md#integer-types
[Language prelude]: #language-prelude
[Machine-dependent integer types]: ../types/numeric.md#machine-dependent-integer-types
[Macro namespace]: namespaces.md
[name resolution]: name-resolution.md
[Standard library prelude]: #standard-library-prelude
[Textual types]: ../types/textual.md
[tool attributes]: ../attributes.md#tool-attributes
[Tool prelude]: #tool-prelude
[Type namespace]: namespaces.md
[use declarations]: ../items/use-declarations.md
