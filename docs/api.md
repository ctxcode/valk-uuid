
# Documentation

Namespaces: [main](#main)

---

# main

## Errors for 'main'

```js
// Thrown when text cannot be read as a UUID.
+ error Error (syntax) payload { message: String }
```

## Functions for 'main'

```js
// Returns whether text reads as a UUID.
+ fn is_valid(text: String) bool
// The id that is all zeroes.
+ fn nil() Uuid
// Reads text as a UUID.
+ fn parse(text: String) Uuid !Error
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
// The 16 bytes of a UUID.
+ struct Uuid {
    // The bytes, in the order they are written.
    + bytes: [u8 x 16]

    // Returns -1, 0 or 1, comparing the bytes in order.
    + fn compare(other: Uuid) int
    // Returns whether two ids are the same. Backs `==`.
    + fn equals(other: Uuid) bool
    // Orders ids by their bytes.
    + fn gt(other: Uuid) bool
    // Hashes the bytes, so an id can be a `HashMap` key.
    + fn hash() uint
    // Returns whether every byte is zero.
    + fn is_nil() bool
    // Orders ids by their bytes, which for version 7 is also the order they were made in.
    + fn lt(other: Uuid) bool
    // Returns the UUID that is all zeroes, which stands for "no id".
    + static fn nil() Uuid
    // Returns the id as 32 hex digits, without the dashes.
    + fn to_hex() String
    // Returns the id as text: 36 characters, lowercase, with the usual dashes.
    + fn to_string() String
    // Returns the id as `urn:uuid:...`.
    + fn to_urn() String
    // Returns the time a version 7 id was made, in milliseconds since the Unix epoch, or 0 for an id of another version.
    + fn unix_ms() uint
    // Returns the version digit: 4 for a random id, 7 for a time-ordered one, 5 for a name based one, and 0 for the nil id.
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
