# Changelog

## 1.1.1

### Added

- Publish package discovery metadata, the README, the changelog, and the MIT
  license with this version.

### Compatibility

- No component API or dependency changes from 1.1.0.

## 1.1.0

### Added

- `Livt.Utils.Hashing.Crc<WIDTH = 32, POLYNOMIAL = 0x04C11DB7, REFLECTED = true>`:
  combinational CRC helpers with compile-time width, polynomial and bit order.
- Bit, byte and bounded bit-sequence updates, register reflection and explicit
  finalization. The caller owns the running accumulator and initial value.
- Widths from 1 through 32 bits, with reflected and forward processing. Polynomial
  parameters use normal notation without the leading term.
- Known-vector and boundary regressions covering multiple CRC profiles, empty
  input, one-bit widths and final reflection.

### Changed

- Document CRC profile configuration, caller-owned state, finalization and
  combinational timing alongside the existing block-oriented `Crc32` API.

### Compatibility and dependencies

- The existing `Crc32` API and implementation remain unchanged.
- `Crc` owns no clock, running state, lookup table or RAM. Callers supply storage
  and scheduling; checksum byte order and protocol framing remain caller choices.
- No package dependencies are required.

## 1.0.2

- Existing CRC32 utility package.
