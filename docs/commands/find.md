# find

## Overview
`find` searches directory trees in real time by evaluating file metadata, paths, and attributes. Unlike index-based search utilities, it queries the live filesystem directly and can execute operations on matched results.

## Common Pitfalls & Edge Cases

* **Unquoted Globs Triggering Shell Expansion:** Passing unquoted wildcards to `-name` (e.g., `find . -name *.log`) causes the current shell to expand the wildcard *before* `find` receives it. If matching files exist in the current working directory, `find` receives multiple arguments and aborts with a syntax error. Always quote patterns: `find . -name "*.log"`.
* **The Raw Byte Suffix Quirk (`c`):** In `find`, standard unit abbreviations differ from utilities like `ls` or `truncate`. To match exact bytes, use the suffix `c` (characters/bytes), not `b` (which denotes 512-byte blocks). For example, `-size 1033c` matches exactly 1033 bytes, whereas `-size 1033` matches 1033 × 512-byte blocks.
* **Implicit Path Traversal:** If no starting path is provided in standard POSIX environments, `find` may fail or require an explicit search root. Modern GNU `find` defaults to the current directory (`.`), but specifying the path explicitly prevents portability bugs across minimal shells.
* **Unbounded `-exec` Overhead:** Using `-exec command {} \;` spawns a distinct process for every matching file, causing heavy CPU overhead on large directory trees. Using `-exec command {} +` aggregates matched files into batched arguments, drastically reducing process creation costs.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-type` | File Type | Filters by node type: `f` (regular file), `d` (directory), `l` (symlink), `s` (socket). |
| `-name` | Name Pattern | Matches file names against a shell glob pattern (case-sensitive). |
| `-iname` | Case-Insensitive Name | Matches file names ignoring case. |
| `-size` | Size Filter | Filters by size using numeric prefixes (`+` larger than, `-` smaller than, or exact) and units (`c` bytes, `k` KiB, `M` MiB, `G` GiB). |
| `-mtime` | Modification Time | Filters files modified $n \times 24$ hours ago (`+n` older than, `-n` newer than). |
| `-perm` | Permission Bits | Matches file permission modes (e.g., `/4000` for SUID, `-002` for world-writable). |
| `-exec` | Action Execution | Executes an external command on each match (`{}` is replaced by the file path). |
| `-delete` | In-Place Removal | Deletes matching files directly within `find` without invoking an external shell process. |

## Production Use Cases

* **Rotating Stale Application Logs:** Automated cron jobs locate and remove uncompressed application logs older than a retention threshold (e.g., 14 days) to prevent disk exhaustion.
* **Security Auditing & Compliance:** System administrators scan mounts to detect world-writable files or unexpected SUID binaries that introduce privilege escalation vectors.

## Production Examples

```bash
# Locate and delete uncompressed logs older than 14 days under /var/log/app
find /var/log/app -type f -name "*.log" -mtime +14 -delete

# Find world-writable files excluding symlinks, batching the output to ls
find /var/www -type f -perm -002 -exec ls -la {} +

# Locate files larger than 500MB across a data mount to troubleshoot disk pressure
find /mnt/data -type f -size +500M -exec du -h {} +