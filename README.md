# Livt.Utils

`Livt.Utils` provides small reusable utility components for Livt hardware
packages. It is intended for practical helpers that are broadly useful but do
not belong in `Livt.Base`, `Livt.IO`, `Livt.Math`, `Livt.Crypto`, or a more
specific domain package.

The 1.0.1 package surface is intentionally small:

- `Livt.Utils.Hashing.Crc32`: CRC32 checksum component using the IEEE 802.3 /
  zlib reflected polynomial `0xEDB88320`.

## 📦 Package

```toml
[dependencies]
Livt.Utils = "1.0.1"
```

`Livt.Utils` currently has no package dependencies.

## 📚 Namespaces

Production components use a domain namespace below `Livt.Utils`. Tests mirror
that shape below `Livt.Utils.Tests`.

| Namespace | Component | Synthesizable | Purpose |
|---|---|---|---|
| `Livt.Utils.Hashing` | `Crc32` | Yes | CRC32 checksum over 16-byte blocks |

## 🔌 API Overview

### CRC32

`Crc32` follows the IEEE 802.3 / zlib CRC32 convention:

- reflected polynomial: `0xEDB88320`
- initial accumulator: `0xFFFFFFFF`
- final XOR: `0xFFFFFFFF`
- check value: `CRC32("123456789") = 0xCBF43926`
- output byte order: little-endian

The component processes data as full 16-byte blocks plus one final partial
block:

- `BLOCK_SIZE = 16`
- `Reset()`
- `ProcessBlock(blk)`
- `ProcessFinalBlock(blk, len)`
- `GetCrc()`
- `GetByte(idx)`

Call `Reset()` before starting a computation. Call `ProcessBlock()` for each
complete 16-byte block. Call `ProcessFinalBlock()` exactly once for the final
`0..16` bytes; this applies the final XOR. Read the result with `GetCrc()` or
`GetByte(idx)`.

`GetByte(0)` returns the least-significant CRC byte and `GetByte(3)` returns
the most-significant CRC byte. The current 1.0.1 behavior returns byte 3 for
`GetByte(idx)` when `idx` is outside `0..3`.

## 🧪 Build and Test

```sh
livt test
```

The configured test list is defined in [`livt.toml`](livt.toml). Usage examples
live in [`docs/usage.md`](docs/usage.md). Hardware notes live in
[`docs/hardware-notes.md`](docs/hardware-notes.md).

## 🛠️ Development Notes

- Keep source components in a domain namespace below `Livt.Utils`.
- Keep tests in the mirrored `Livt.Utils.Tests.<Domain>` namespace.
- Keep utility components general-purpose and domain-neutral.
- Move cryptographic primitives to `Livt.Crypto`.
- Move arithmetic primitives to `Livt.Math`.
- Move I/O and peripheral components to `Livt.IO`.
- Document reproducible compiler issues in `COMPILER.md`.

## 🚧 Outlook

Future additions may include CRC32C, CRC16 variants, Adler-32, Fletcher
checksums, byte-order helpers, and other small utilities that are reusable
across Livt packages.

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
