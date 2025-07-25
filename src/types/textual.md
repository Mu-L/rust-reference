r[type.text]
# Textual types

r[type.text.intro]
The types `char` and `str` hold textual data.

r[type.text.char-value]
A value of type `char` is a [Unicode scalar value] (i.e. a code point that is
not a surrogate), represented as a 32-bit unsigned word in the 0x0000 to 0xD7FF
or 0xE000 to 0x10FFFF range.

r[type.text.char-precondition]
It is immediate [undefined behavior] to create a
`char` that falls outside this range. A `[char]` is effectively a UCS-4 / UTF-32
string of length 1.

r[type.text.str-value]
A value of type `str` is represented the same way as `[u8]`, a slice of
8-bit unsigned bytes. However, the Rust standard library makes extra assumptions
about `str`: methods working on `str` assume and ensure that the data in there
is valid UTF-8. Calling a `str` method with a non-UTF-8 buffer can cause
[undefined behavior] now or in the future.

r[type.text.str-unsized]
Since `str` is a [dynamically sized type], it can only be instantiated through a
pointer type, such as `&str`. The layout of `&str` is the same as the layout of
`&[u8]`.

r[type.text.layout]
## Layout and bit validity

r[type.layout.char-layout]
`char` is guaranteed to have the same size and alignment as `u32` on all platforms.

r[type.layout.char-validity]
Every byte of a `char` is guaranteed to be initialized (in other words,
`transmute::<char, [u8; size_of::<char>()]>(...)` is always sound -- but since
some bit patterns are invalid `char`s, the inverse is not always sound).

[Unicode scalar value]: http://www.unicode.org/glossary/#unicode_scalar_value
[undefined behavior]: ../behavior-considered-undefined.md
[dynamically sized type]: ../dynamically-sized-types.md
