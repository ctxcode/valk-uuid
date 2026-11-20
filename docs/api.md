
# Documentation

Namespaces: [main](#main)

---

# main

## Errors for 'main'

```js
// Thrown when text or bytes cannot be read as a UUID or ULID.
+ error Error (syntax, length) payload { message: String }
```

## Functions for 'main'

```js
// Reads 16 bytes as a UUID, such as a binary column from a database.
+ fn from_bytes(bytes: local &[u8]) Uuid !Error
// Returns whether text reads as a UUID.
+ fn is_valid(text: String) bool
// Returns whether text parses as a valid ULID.
+ fn is_valid_ulid(text: String) bool
// The id that is all ones.
+ fn max() Uuid
// The id that is all zeroes.
+ fn nil() Uuid
// Reads text as a UUID.
+ fn parse(text: String) Uuid !Error
// Parses a 26-character Crockford Base32 string as a ULID (case-insensitive).
+ fn parse_ulid(text: String) Ulid !Error
// Returns a new time-ordered ULID.
+ fn ulid() Ulid
// Returns a new ULID as its 26-character Crockford Base32 string.
+ fn ulid_string() String
// Returns the id a name has inside a namespace, using MD5: version 3.
+ fn v3(namespace: Uuid, name: String) Uuid
// Returns a random id: version 4, 122 bits from the system's random source.
+ fn v4() Uuid
// Returns a random id as text, for the many places that only want the string.
+ fn v4_string() String
// Returns the id a name has inside a namespace: version 5, the SHA-1 of the two.
+ fn v5(namespace: Uuid, name: String) Uuid
// Returns a time-ordered id: version 7, the millisecond it was made followed by random bits.
+ fn v7() Uuid
// Returns a time-ordered id as text.
+ fn v7_string() String
```

## Classes for 'main'

```js
// The 16 bytes of a ULID (Universally Unique Lexicographically Sortable Identifier).
+ struct Ulid {
    // The 16 bytes, in big-endian order (6 bytes timestamp + 10 bytes randomness).
    + bytes: [u8 x 16]

    // Returns -1, 0 or 1, comparing the bytes in order.
    + fn compare(other: Ulid) int
    // Returns whether two ULIDs are equal. Backs `==`.
    + fn equals(other: Ulid) bool
    // Reads a `Ulid` from a JSON string, for `valk.json`'s `to_type` and `decode_to`.
    + static fn from_json_value_auto[X](value: X) Ulid !LookupError
    // Orders ULIDs by their bytes.
    + fn gt(other: Ulid) bool
    // Hashes the bytes, so a ULID can be a `HashMap` key.
    + fn hash() uint
    // Returns whether every byte is 0xff.
    + fn is_max() bool
    // Returns whether every byte is zero.
    + fn is_nil() bool
    // Orders ULIDs by their bytes (matching chronological and string order).
    + fn lt(other: Ulid) bool
    // Returns the largest ULID (`7ZZZZZZZZZZZZZZZZZZZZZZZZZ`).
    + static fn max() Ulid
    // Returns the all-zero ULID (`00000000000000000000000000`).
    + static fn nil() Ulid
    // Returns `to_string()`: `valk.json` writes a `Ulid` as a 26-character JSON string.
    + fn to_json_string() String
    // Encodes the ULID as 26 uppercase Crockford Base32 characters.
    + fn to_string() String
    // Reinterprets the 16 bytes as a UUID.
    + fn to_uuid() Uuid
    // Returns the timestamp embedded in the ULID, in milliseconds since the Unix epoch.
    + fn unix_ms() uint
}
```

```js
// The 16 bytes of a UUID.
+ struct Uuid {
    // The bytes, in the order they are written.
    + bytes: [u8 x 16]

    // Returns -1, 0 or 1, comparing the bytes in order.
    + fn compare(other: Uuid) int
    // Returns whether two ids are the same. Backs `==`.
    + fn equals(other: Uuid) bool
    // Reads a `Uuid` from a JSON string, for `valk.json`'s `to_type` and `decode_to`.
    + static fn from_json_value_auto[X](value: X) Uuid !LookupError
    // Orders ids by their bytes.
    + fn gt(other: Uuid) bool
    // Hashes the bytes, so an id can be a `HashMap` key.
    + fn hash() uint
    // Returns whether every byte is 0xff.
    + fn is_max() bool
    // Returns whether every byte is zero.
    + fn is_nil() bool
    // Orders ids by their bytes, which for version 7 is also the order they were made in.
    + fn lt(other: Uuid) bool
    // Returns the UUID that is all ones, which the standard reserves as the largest id.
    + static fn max() Uuid
    // Returns the UUID that is all zeroes, which stands for "no id".
    + static fn nil() Uuid
    // Returns the id as 32 hex digits, without the dashes.
    + fn to_hex() String
    // Returns `to_string()`: `valk.json` writes a `Uuid` as a canonical JSON string.
    + fn to_json_string() String
    // Returns the id as text: 36 characters, lowercase, with the usual dashes.
    + fn to_string() String
    // Reinterprets the 16 bytes as a ULID.
    + fn to_ulid() Ulid
    // Returns the id as `urn:uuid:...`.
    + fn to_urn() String
    // Returns the time a version 7 id was made, in milliseconds since the Unix epoch, or 0 for an id of another version.
    + fn unix_ms() uint
    // Returns the version digit: 4 for a random id, 7 for a time-ordered one, 5 for a name based one, and 0 for the nil and max ids.
    + fn version() uint
}
```

## Globals for 'main'

```js
// The namespaces the standard defines for `v5` and `v3`.
+ global NAMESPACE_DNS : Uuid
// The namespace for ISO OIDs.
+ global NAMESPACE_OID : Uuid
// The namespace for URLs.
+ global NAMESPACE_URL : Uuid
// The namespace for X.500 names.
+ global NAMESPACE_X500 : Uuid
```
