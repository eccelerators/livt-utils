# Generic CRC calculation

`Livt.Utils.Hashing.Crc<WIDTH = 32, POLYNOMIAL = 0x04C11DB7, REFLECTED = true>`
provides context-free helpers. Each specialization owns no running state, clock,
lookup table or RAM. The caller owns its `logic[WIDTH]` accumulator and seeds it
explicitly at each new message. This component is available from version 1.1.0;
the existing stateful `Crc32` API remains unchanged.

The polynomial is in normal notation, excluding the leading x^WIDTH term.
`REFLECTED` chooses the register shift direction and byte input order. A reflected
profile consumes bit 0 first; a forward profile consumes bit 7 first.

| Operation | Contract |
|---|---|
| `UpdateBit(crc, value)` | Consume one bit in transmission order |
| `UpdateByte(crc, value)` | Consume all eight bits in the selected order |
| `UpdateBits(crc, value, count)` | Consume 0..32 low-order bits without byte padding; reflected starts at bit 0, forward at bit count-1 |
| `Reflect(value)` | Reverse exactly WIDTH bits |
| `Finalize(crc, xorOut, reverse)` | Optionally reverse the register, then XOR; does not mutate state or serialize bytes |

WIDTH must be 1..32 and the nonzero polynomial must fit WIDTH bits. Supply
`count` in 0..32. Finalization reflection is **relative to the running register**:
CRC catalogue profiles whose RefIn and RefOut agree use `reverse = false`.
Byte serialization is the protocol caller's responsibility. Never feed the
finalized checksum back into an update. For verification by residue, update the
raw state with the received checksum bytes and compare before finalization.

```livt
checksum: Crc<16, 0x8005, true>
running: logic[16] = 0xFFFF

new()
{
    checksum = new Crc<16, 0x8005, true>()
}

// Within the caller's clocked state, only when accepting an input byte:
// running = checksum.UpdateByte(running, value)
// Final result: checksum.Finalize(running, 0xFFFF, false)
```

These helpers lower to combinational logic. They introduce no scheduled call
handshake, but their propagation delay still belongs in the caller's timing
budget. `UpdateBits` has a bounded 32-iteration implementation; constant counts
allow synthesis to eliminate unused steps. Do not infer a universal clock-rate
promise from a successful functional test.

Packet framing, checksum field coverage, byte serialization and protocol error
handling belong to the consuming protocol component.
