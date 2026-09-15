# tr

## Overview
`tr` translates, squeezes, or deletes individual characters from standard input and writes the result to standard output. In systems administration and shell automation, it normalizes line breaks, sanitizes input delimiters, and modifies character case across streaming pipelines.

## Common Pitfalls & Edge Cases

* **No File Path Arguments:** `tr` does not accept file paths as positional arguments (e.g., `tr 'a' 'b' file.txt` fails or misinterprets arguments). It operates strictly on standard input. Always feed input via pipes (`cat file.txt | tr ...`) or shell redirection (`tr ... < file.txt`).
* **Asymmetric Set Lengths:** If `SET1` contains more characters than `SET2`, GNU `tr` repeats the final character of `SET2` until lengths match, while other UNIX implementations truncate the excess. To avoid unexpected mappings, always ensure `SET1` and `SET2` have identical lengths unless using `-d` or `-t`.
* **Multi-Byte UTF-8 Limitations:** Standard implementations of `tr` operate on 1-byte (8-bit) ASCII characters. Supplying multi-byte Unicode characters (such as emojis or accented characters) can mangle bytes mid-stream. Use `sed` or `awk` when manipulating multi-byte UTF-8 data.
* **Over-Aggressive Deletion:** The delete flag (`-d`) removes *every* instance of every character in `SET1` independently, rather than matching a multi-character string. For example, `tr -d 'foo'` removes all `f` and `o` characters across the entire stream.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-d` | Delete | Deletes all input characters listed in `SET1`. |
| `-s` | Squeeze Repeats | Replaces consecutive sequences of a repeated character with a single occurrence. |
| `-c` / `-C` | Complement | Operates on the complement (inverse) of `SET1`. |
| `-t` | Truncate Set 1 | Truncates `SET1` to the length of `SET2` before translating. |

## Production Use Cases

* **Sanitizing Windows Line Endings (CRLF to LF):** SREs strip carriage return characters (`\r` or `\015`) from shell scripts or configuration templates created in Windows before deploying them to Linux hosts.
* **Stream Delimiter Normalization:** Pipelines convert multi-space log fields into single delimiters or newlines for uniform processing with `cut`, `sort`, or `awk`.

## Production Examples

```bash
# Strip Windows carriage returns (\r) from a shell script or configuration file
tr -d '\r' < entrypoint.sh > entrypoint-clean.sh

# Convert all lowercase characters to uppercase across a stream
echo "env=production;tier=web" | tr '[:lower:]' '[:upper:]'

# Squeeze repeated whitespace into single spaces to normalize unaligned logs
ps aux | tr -s ' '

# Convert comma-separated values into newline-delimited rows
echo "web01,web02,web03,api01" | tr ',' '\n'

# Squeeze and replace all non-alphanumeric characters with newlines
cat access.log | tr -cs '[:alnum:]' '\n'