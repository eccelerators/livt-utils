# Livt.Utils Usage

## Choosing a CRC interface

Use `Crc` for configurable widths and polynomials with caller-owned state.
Initialize the accumulator at the start of each message, pass its raw value to
`UpdateBit()`, `UpdateByte()` or `UpdateBits()`, and apply `Finalize()` when the
message is complete. The [generic CRC guide](crc.md) contains a configuration
example and explains reflection and final XOR.

Use `Crc32` for stateful CRC-32/ISO-HDLC processing through its scheduled 16-byte
block API. Its usage is unchanged in 1.1.0; the following examples use that API.

## CRC32 for One Short Message

```livt
using Livt.Utils.Hashing

component Crc32Example
{
    crc: Crc32

    new()
    {
        this.crc = new Crc32()
    }

    public fn ComputeCheckValue() byte[4]
    {
        this.crc.Reset()

        var blk: byte[16] = [
            0x31, 0x32, 0x33, 0x34,
            0x35, 0x36, 0x37, 0x38,
            0x39, 0x00, 0x00, 0x00,
            0x00, 0x00, 0x00, 0x00]

        this.crc.ProcessFinalBlock(blk, 9)
        return this.crc.GetCrc()
    }
}
```

The returned bytes are little-endian. For `"123456789"` the expected bytes are
`0x26, 0x39, 0xF4, 0xCB`.

## CRC32 for Multiple Blocks

```livt
using Livt.Utils.Hashing

component Crc32StreamExample
{
    crc: Crc32

    new()
    {
        this.crc = new Crc32()
    }

    public fn Compute(first: byte[16], last: byte[16], lastLength: int) byte[4]
    {
        this.crc.Reset()
        this.crc.ProcessBlock(first)
        this.crc.ProcessFinalBlock(last, lastLength)
        return this.crc.GetCrc()
    }
}
```

Call `ProcessFinalBlock()` once at the end of the message. Passing `len = 0`
finalizes the current accumulator without adding more data.
