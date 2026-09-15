# sort

## Overview
`sort` reorders lines of text files or piped standard input streams according to specified comparison criteria. In systems operations and data pipelines, it organizes structured logs, extracts metrics, and arranges input for downstream utilities like `uniq` and `comm`.

## Common Pitfalls & Edge Cases

* **Lexicographical vs. Numerical Sorting Defaults:** By default, `sort` performs an ASCII/lexicographical sort. This ranks `10` before `2` because the character `'1'` precedes `'2'`. Always specify `-n` for integer comparison or `-h` for human-readable data units (e.g., `2K`, `10M`, `4G`).
* **Field Delimiter Whitespace Normalization:** By default, `sort` treats contiguous spaces and tabs as field separators, often shifting expected column offsets. When parsing comma-, tab-, or colon-separated datasets, always set an explicit delimiter using `-t` (e.g., `-t ','` or `-t ':'`).
* **Locale-Induced Sorting Inconsistencies:** System locale settings (`LC_COLLATE`) can alter sort ordering, case sensitivity, and dictionary collation rules across different environments. Prefix sorting pipelines with `LC_ALL=C` to enforce deterministic, byte-order POSIX collation and maximize processing speed.
* **Disk Spills on High-Volume Datasets:** When sorting multi-gigabyte files exceeding available RAM, `sort` spills temporary chunks to `/tmp`. If `/tmp` runs out of inode or storage space, the process crashes. Use `-T /path/to/custom_tmp` and allocate memory thresholds with `-S` (`--buffer-size`) to control I/O performance.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-n` | Numeric Sort | Compares fields according to their numerical integer value rather than lexicographical order. |
| `-h` | Human Numeric Sort | Compares human-readable storage units (e.g., `2K`, `500M`, `4G`). |
| `-r` | Reverse | Inverts the sort result order to descending. |
| `-k` | Key Definition | Restricts sorting comparison to a specific field position (`-k POS1[,POS2]`). |
| `-t` | Field Separator | Overrides the default whitespace delimiter with an explicit character separator. |
| `-u` | Unique | Suppresses all duplicate lines that compare equal, outputting only unique entries. |
| `-c` | Check Sorted | Verifies whether the input is already sorted and exits non-zero if unsorted. |
| `-S` | Buffer Size | Sets the maximum main memory buffer size (e.g., `-S 2G`) before spilling to disk. |
| `-T` | Temporary Directory | Specifies a custom filesystem location for temporary scratch files during large sorts. |

## Production Use Cases

* **Analyzing Top Web Traffic Metrics:** Engineers sort parsed access log records to rank top client IP addresses, heaviest URL endpoints, or highest request latencies during traffic anomalies.
* **Stream Preparation for Deduplication:** DevOps pipelines sort unsorted system streams prior to feeding them into adjacency-dependent tools such as `uniq` or `join`.

## Production Examples

```bash
# Sort a colon-separated file (like /etc/passwd) numerically by the UID in column 3
sort -t ':' -k 3,3n /etc/passwd

# Sort disk space usage reports by human-readable sizes in descending order
du -sh /var/log/* | sort -rh

# Enforce deterministic POSIX byte-order sorting for reproducible builds
LC_ALL=C sort -u package_manifest.txt

# Sort a large CSV log file by timestamp in column 1, allocating 2GB RAM buffer
sort -t ',' -k 1,1 -S 2G -T /mnt/scratch access_records.csv