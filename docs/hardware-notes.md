# Livt.Utils Hardware Notes

## CRC32

`Crc32` is a stateful checksum component. It stores a 32-bit accumulator as four
bytes and updates the accumulator one input byte at a time.

The public data path is fixed:

- input block width: 16 bytes
- final block length: `0..16`
- result width: 4 bytes
- result byte order: little-endian

## Lookup Tables

The CRC table is stored as private byte constants split into four byte lanes and
four 64-entry chunks per lane. This keeps each lookup small and avoids exposing
implementation tables as part of the package API.

The table content corresponds to the reflected CRC32 polynomial `0xEDB88320`.

## Timing

The implementation is FSM-oriented. Each input byte performs four table lookups
and one state update. Approximate functional-simulation cycle counts are:

| Operation | Approx. cycles |
|---|---|
| `Reset()` | 1 |
| `ProcessBlock(16 bytes)` | about 320-400 |
| `ProcessFinalBlock(n bytes)` | about `n * 20-25 + 5` |
| `GetByte()` / `GetCrc()` | 1 |

At 100 MHz this is roughly 4-5 MB/s for continuous `ProcessBlock()` calls.
Exact hardware timing depends on the target FPGA and synthesis toolchain.

## Scope

`Crc32` is suitable for non-cryptographic data integrity checks such as Ethernet
FCS-compatible CRCs, PNG, ZIP, and zlib-style checksums. It is not a
cryptographic authentication primitive.
