# file

## Overview
`file` determines the data type and format of a target file by inspecting its header, structure, and magic numbers. It identifies file contents independently of file extensions or user-provided filenames.

## Common Pitfalls & Edge Cases

* **Leading Dashes Interpreted as Flags:** When running `file *` in a directory containing files named like `-file01`, `file` parses `-file01` as command-line options and terminates with an error. Always prefix relative paths with `./` (e.g., `file ./*`) or terminate option parsing with `--` (e.g., `file -- *`).
* **Extension Spoofing Assumption:** In Linux, file extensions have no system-level enforcement. A file named `payload.txt` can contain an ELF binary, and a file named `image.png` can contain PHP code. Relying on extensions instead of `file` checks creates severe security blindspots.
* **Symlink Dereferencing:** By default, `file` inspects the symbolic link itself and reports `symbolic link to ...`, rather than the destination file type. Use `-L` or `-h` to control whether the link or target is inspected.
* **Compressed File Transparency:** Without specific flags, `file` only reports the outer compression envelope (e.g., `gzip compressed data`) rather than identifying what archive format or payload sits inside the compressed stream.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-b` | Brief Mode | Suppresses the filename prefix in the output, printing only the identified file type. |
| `-i` | MIME Type | Outputs MIME type strings (e.g., `text/plain`, `application/pdf`) and character sets instead of human-readable descriptions. |
| `-z` | Look Inside Compressed | Attempts to decompress and inspect the contents of compressed files (e.g., gzip, bzip2). |
| `-L` | Dereference Symlinks | Follows symlinks and identifies the target file rather than reporting the symlink node. |
| `-k` | Keep Going | Disables early termination on the first match and prints all matching format signatures. |

## Production Use Cases

* **Validating File Uploads:** Web application backends and API gateways use `file -i` to verify that uploaded assets match their declared MIME types before saving them to storage buckets, preventing malicious script uploads disguised as images.
* **Incident Triage & Forensic Analysis:** Security responders inspect unverified binary dumps, suspicious cron payloads, or dropped rootkits on compromised hosts to classify file architecture and detect obfuscated executables.

## Production Examples

```bash
# Output clean MIME type strings for upload payload validation
file -b --mime-type ./uploaded-asset

# Inspect files in a directory safely, preventing leading-dash argument bugs
file ./*

# Follow symlinks to determine real underlying target formats
file -L /usr/bin/python3

# Inspect the uncompressed payload inside a compressed archive
file -z /var/log/syslog.2.gz