# head

## Overview
`head` outputs the initial portion of files or piped streams to standard output. In systems engineering and data operations, it provides rapid sampling of schemas, headers, and pipeline stream verification without loading massive files into memory.

## Common Pitfalls & Edge Cases

* **The Negative Line Offset Syntax (`-n -K`):** Passing a negative integer to `-n` instructs `head` to output all lines *except* the trailing $K$ lines of the file. This contrasts with `tail -n +K` (which starts at line $K$) and can produce massive, unexpected terminal output if run on large files under the mistaken assumption that it prints the last lines.
* **Pipeline Early Termination (`SIGPIPE` / Broken Pipe):** When `head` reads its requested line count from a piped producer, it closes its file descriptor and terminates immediately. This triggers a `SIGPIPE` (Exit Code 141) in the upstream process. In strict shell scripts (`set -e -o pipefail`), unhandled broken pipes will crash the script unexpectedly.
* **Multi-File Header Pollution:** Like `tail`, running `head` against multiple files injects banner headers (`==> filename <==`). When sampling data to validate format consistency, these banners corrupt structured pipelines unless suppressed with `-q` (`--quiet`).
* **Binary File Slicing:** Extracting file headers using `-n` on binary files splits on newline byte values (`0x0A`), which can corrupt magic numbers or header structures. Always use byte mode (`-c`) when carving out binary file headers.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-n` | Output Lines | Outputs the first $K$ lines (or all lines except the last $K$ with `-K`). |
| `-c` | Output Bytes | Outputs the first $K$ bytes (or all bytes except the last $K$ with `-K`). |
| `-q` | Quiet Mode | Suppresses file header banners when reading multiple target files. |
| `-v` | Verbose Mode | Forces header banners to print even when inspecting a single file. |

## Production Use Cases

* **Inspecting Data Pipeline Schemas:** Data and platform engineers sample the first 5–10 lines of gigabyte-scale CSVs or database dumps to inspect column headers and delimiter structure without risking out-of-memory errors.
* **Extracting Binary File Signatures (Magic Bytes):** DevOps engineers extract the initial bytes of unverified payloads or disk images using byte mode (`-c`) to inspect format headers directly with `xxd` or `hexdump`.

## Production Examples

```bash
# Sample column headers and first records of a large data dump
head -n 5 /mnt/data/analytics_export.csv

# Inspect headers across multiple configuration files without banner clutter
head -q -n 2 /etc/nginx/conf.d/*.conf

# Carve out the first 16 bytes of an unknown payload for hex inspection
head -c 16 ./unknown_blob | hexdump -C

# Strip trailing metadata lines from a file (print everything except the last 3 lines)
head -n -3 /var/log/summary.txt