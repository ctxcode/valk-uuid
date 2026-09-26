
# valk-uuid

UUIDs for [Valk](https://valk-lang.dev): random (version 4), time-ordered (version 7) and name
based (version 5 and 3), with reading and writing of every form they are written in. Purely
written in Valk, with no os-package dependencies.

Requires Valk 0.7.0 or newer.

## Install

```
vman install github.com/ctxcode/valk-uuid
```

## Example

```rust
use uuid

let id = uuid.v7()                       // time ordered, good as a primary key
println(id.to_string())                  // 019283a1-9c4e-7c3e-8f12-6a0b1d2e3f40
println(id.version())                    // 7
println(id.unix_ms())                    // when it was made

let random = uuid.v4()                   // random, when nothing should be readable from it
let derived = uuid.v5(uuid.NAMESPACE_URL, "https://valk-lang.dev")   // always the same id

let parsed = uuid.parse(text) ! panic("%{E.message}")
if uuid.is_valid(text) : println("that is an id")
```

`uuid.v4_string()` and `uuid.v7_string()` are there for the many places that only want the text.

## Which version to use

**Version 7** is the one to reach for in a database. The first 48 bits are the millisecond it
was made, so ids sort by age and new rows land next to each other in the index, where version 4
ids scatter across it and make the index slower to write and larger. Ids made on one thread
always come out in order, also within one millisecond and when the clock steps back.

**Version 4** is 122 random bits, for ids that must say nothing at all — not even roughly when
they were made.

**Version 5** is the SHA-1 of a namespace and a name, so the same name always gives the same id,
on every machine and every run. `NAMESPACE_DNS`, `NAMESPACE_URL`, `NAMESPACE_OID` and
`NAMESPACE_X500` are the namespaces the standard defines, and any id can be a namespace of your
own. **Version 3** is the same with MD5, for ids that were made that way before.

## The id itself

`Uuid` is a struct of 16 bytes, passed and stored by value.

```rust
id.to_string()     // 36 characters, lowercase, with dashes
id.to_hex()        // 32 digits, no dashes
id.to_urn()        // urn:uuid:...
id.bytes           // the 16 bytes
id.version()       // 4, 7, 5, 3, or 0 for the nil id
id.is_nil()        // whether every byte is zero
id.unix_ms()       // when a version 7 id was made; 0 for the others
```

Ids compare with `==`, order with `<` and `>` by their bytes — which for version 7 is the order
they were made in — and hash, so they work as `HashMap` keys and `Array.sort` puts them in
order.

## Reading text

`parse` takes the 36 character form, the 32 digits without dashes, a `urn:uuid:` in front, and
braces around it, in upper or lower case. It throws `syntax` with a message naming the text when
it is none of those; `is_valid` answers the same question with a bool.

## Development

`make test` runs the suite, `make example` runs the example, `make lint` checks the sources and
`make docs` regenerates the API documentation.
