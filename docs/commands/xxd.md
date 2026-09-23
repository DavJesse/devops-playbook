# xxd

## Overview
Generates a formatted hexadecimal dump of a binary file or standard input, and reverses hex dumps back into raw binary representations. DevOps engineers and systems administrators use it to triage binary corruption, inspect raw packet payloads, and patch compiled artifacts or configuration state files.

## Common Pitfalls & Edge Cases
- **Trailing newlines breaking binary reversal:** Piped strings using `echo` include an implicit trailing newline (`0x0A`). Reversing piped text with `xxd -r -p` into a binary file will append unwanted newline bytes unless you pass `echo -n` or feed raw streams via `printf`.
- **Incompatible plain dump formats with offset mode:** The plain hex dump flag (`-p` / `--ps`) creates a continuous stream without address offsets or ASCII columns. Combining plain mode flags inconsistently during reversal (`-r`) will result in truncated or malformed binary targets.
- **Endianness confusion with grouped displays (`-e`):** Using little-endian format (`-e`) rearranges the visual byte ordering in memory representation clusters (4-byte chunks). Reversing a dump generated with `-e` without matching options creates inverted, unreadable byte sequences.
- **Unbounded terminal output on large targets:** Running `xxd` directly against multi-megabyte binary dumps floods terminal stdout and blocks buffer pipelines. Always limit byte length via `-l` or seek through files using `-s`.

## Essential Flags
| Flag | Name | Function |
|---|---|---|
| `-r` | `--reverse` | Reverses a hex dump back into binary data. |
| `-p` | `--ps` | Outputs continuous plain hexadecimal bytes with no offset prefixes or ASCII translation. |
| `-l LEN` | `--length` | Stops reading after outputting `LEN` octets/bytes. |
| `-s OFFSET` | `--seek` | Starts reading from file offset `OFFSET` (supports absolute values or `+`/`-` relative jumps). |
| `-c COLS` | `--cols` | Formats output to display `COLS` octets per line (default is 16). |
| `-b` | `--bits` | Prints bits (binary representation: 0s and 1s) instead of hex digits. |
| `-e` | `--endian` | Dumps data in 32-bit little-endian word clusters instead of standard big-endian octets. |

## Production Use Cases
Systems engineers reach for `xxd` to extract and inspect magic bytes from uploaded artifacts or network buffers to verify file types at the byte level, and to perform precise, in-place binary patching inside CI/CD test fixtures without installing heavy disassembly tools.

## Production Examples

```bash
# Inspect the first 16 magic bytes of an unknown payload in hex and ASCII
xxd -l 16 /var/payloads/incoming.dat

# Dump a raw binary secret to a flat hex string without line numbering or spaces
xxd -p -c 256 /etc/ssl/certs/seed.bin

# Safely reverse an arbitrary hex string into a pure binary file (no trailing newlines)
printf "48656c6c6f20576f726c640a" | xxd -r -p > output.bin

# Jump directly to a byte offset (e.g. 1024) to inspect 32 bytes of a disk image header
xxd -s 1024 -l 32 /dev/sdb1

# Generate a C-style static array initialization directly from a micro-binary asset
xxd -i ./firmware_blob.bin