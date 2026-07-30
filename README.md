# Struct - Ecko Std Lib Package

Pack and unpack binary data with a struct-style format string, for
[Ecko](https://ecko.sh) - written in Ecko, over the `bytes` type. Handy for
binary file formats and wire protocols (the kind of framing the redis/postgres
clients otherwise hand-roll).

Built on `bytes` and the word operators (`band`/`shl`/`shr`/`bor`).

## Install

```bash
ecko get github.com/ecko-lang/struct
```

## Usage

```ecko
import struct

# big-endian: uint32, uint16, uint8
b = struct.pack(">IHB", [65536, 256, 7])     # -> 7 bytes
fields = struct.unpack(">IHB", b)            # -> [65536, 256, 7]

struct.sizeof(">IHBq")                        # 15
```

## Format

An optional endian prefix, then one type code per value:

| Prefix | Meaning |
|---|---|
| `>` | big-endian (**default**) |
| `<` | little-endian |
| `!` | network order (big-endian) |

| Code | Type | Bytes | | Code | Type | Bytes |
|---|---|---|---|---|---|---|
| `b` / `B` | int8 / uint8 | 1 | | `i` / `I` | int32 / uint32 | 4 |
| `h` / `H` | int16 / uint16 | 2 | | `q` / `Q` | int64 / uint64 | 8 |
| `x` | pad byte (skipped) | 1 | | | | |

Lowercase is signed, uppercase unsigned.

## API

| Function | Description |
|---|---|
| `pack(fmt, values)` | Encode a list of integers to `bytes` |
| `unpack(fmt, data)` | Decode `bytes` to a list of integers |
| `sizeof(fmt)` | Total byte count of a format |

## Notes

- Signed values round-trip correctly at every width (two's complement).
- Ecko's `Int` is `i64`, so a `Q` (uint64) value above `i64::MAX` comes back
  negative - its raw bit pattern. This is the one place the abstraction leaks.
- An unknown format code raises a kind-`"value"` error.

## Testing

```bash
ecko test tests/
```

Offline and deterministic. `example.ecko` builds and reads a small packet header.

## License

MIT - see [LICENSE](LICENSE).
