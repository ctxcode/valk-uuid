
# valk-uuid

UUIDs and ULIDs for [Valk](https://valk-lang.dev): random (version 4), time-ordered (version 7),
name-based (version 5 and 3) UUIDs, monotonic Crockford Base32 ULIDs, and native `valk.json`
serialization. Purely written in Valk, with no os-package dependencies.

Requires Valk 0.8.10 or newer.

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

let ulid = uuid.ulid()                   // 26-char Crockford Base32, time ordered
println(ulid.to_string())                // 01ARZ3NDEKTSV4RRFFQ69G5FAV
println(ulid.unix_ms())
```

`uuid.v4_string()`, `uuid.v7_string()`, and `uuid.ulid_string()` are there for the many places that only want the text.

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

`Uuid` is a struct of 16 bytes, passed and stored by value. `uuid.nil()` and `uuid.max()` are
the all-zero and all-one ids, and `uuid.from_bytes(bytes)` reads 16 bytes, throwing `length` for
any other count.

```rust
id.to_string()     // 36 characters, lowercase, with dashes
id.to_hex()        // 32 digits, no dashes
id.to_urn()        // urn:uuid:...
id.bytes           // the 16 bytes
id.version()       // 4, 7, 5, 3, or 0 for the nil and max ids
id.is_nil()        // whether every byte is zero
id.is_max()        // whether every byte is 0xff
id.unix_ms()       // when a version 7 id was made; 0 for the others
id.to_ulid()       // reinterpret the 16 bytes as a Ulid
```

Ids compare with `==`, order with `<` and `>` by their bytes — which for version 7 is the order
they were made in — and hash, so they work as `HashMap` keys and `Array.sort` puts them in
order.

## ULIDs

`Ulid` is a 16-byte struct (48-bit Unix millisecond timestamp + 80 bits of randomness) encoded as
26 uppercase Crockford Base32 characters. `uuid.ulid()` is monotonic on the same thread within a
millisecond, and `Ulid` converts losslessly to and from `Uuid` via `to_uuid()` and `Uuid.to_ulid()`.

```rust
let u = uuid.ulid()
let s = u.to_string()                    // 26-char Crockford Base32
let parsed = uuid.parse_ulid(s) ! panic("%{E.message}")
let valid = uuid.is_valid_ulid(s)
let as_uuid = u.to_uuid()
```

## JSON

Both `Uuid` and `Ulid` implement `to_json_string()` and `from_json_value_auto`, so fields of type
`uuid.Uuid`, `?uuid.Uuid`, `uuid.Ulid`, and `?uuid.Ulid` serialize as strings and decode back
directly with `valk.json`:

```rust
use uuid
use valk.json

class Event {
    id: uuid.Uuid
    trace_id: uuid.Ulid
    parent_id: ?uuid.Uuid
}
```

## Reading text

`parse` takes the 36 character form (dashes only at their usual places), the 32 digits without dashes, a `urn:uuid:` in front, and
braces around it, in any case. It throws `syntax` with a message naming the text when
it is none of those; `is_valid` answers the same question with a bool.

## Development

`make test` runs the suite, `make example` runs the example, `make lint` checks the sources and
`make docs` regenerates the API documentation.
