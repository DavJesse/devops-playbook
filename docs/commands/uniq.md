# uniq

## Overview
`uniq` filters out or reports repeated lines from an input text stream or file. In production administration, it quantifies line frequencies, detects duplicates, and isolates distinct operational events across structured system outputs.

## Common Pitfalls & Edge Cases

* **The Adjacency Requirement:** `uniq` only detects and removes duplicate lines that sit immediately next to each other. Identical lines scattered across a file pass through unmerged. Input must pass through `sort` first to ensure all matching lines are adjacent.
* **Mutually Exclusive Inversion (`-u` vs `-d`):** The unique flag (`-u`) prints lines that appear *only once*, whereas the duplicate flag (`-d`) prints lines that appear *two or more times*. Running `uniq -u -d` cancels out all output and yields an empty result.
* **Unstructured Field Skipping Limitations (`-f`):** The field-skip option (`-f N`) defines fields using blank spaces and tabs only. It cannot parse custom separators like commas or colons. When analyzing structured CSV or colon-delimited files, pre-process columns with `cut` or `awk` before piping to `uniq`.
* **Case-Insensitive Collisions:** Default comparisons evaluate byte parity. In mixed-case streams (e.g., HTTP headers like `Content-Type` vs `content-type`), duplicates will not collapse unless explicit case insensitivity (`-i`) is enabled.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-c` | Count Occurrences | Prefixes each output line with its repetition count in the input stream. |
| `-d` | Repeated Lines Only | Outputs only lines that appear two or more times, discarding singleton lines. |
| `-D` | Print All Duplicates | Prints all duplicate instances rather than a single representative line. |
| `-u` | Unique Lines Only | Outputs only lines that appear exactly once in the stream, discarding repeats. |
| `-i` | Ignore Case | Disables case-sensitive comparisons during line equality checks. |
| `-f` | Skip Fields | Skips the first $N$ whitespace-delimited fields before comparing line contents. |
| `-s` | Skip Characters | Skips the first $N$ characters of each line before evaluating line equality. |
| `-w` | Compare Width | Compares no more than $N$ characters per line for equality evaluation. |

## Production Use Cases

* **Aggregating Traffic Anomalies:** Security teams tally and rank client IP addresses from authentication and firewall logs to detect brute-force surges or DDoS attack patterns.
* **Detecting Data Pipeline Collisions:** Platform engineers locate duplicate primary keys or repeated database export records by isolating multi-occurrence rows before bulk inserts.

## Production Examples

```bash
# Count and rank the top 10 requesting IP addresses from an access log
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head -n 10

# Print only distinct lines that occur strictly once in a dataset
sort transaction_ids.txt | uniq -u

# Identify duplicate user records by ignoring the first column (timestamp)
sort -k 2 audit_events.log | uniq -d -f 1

# Case-insensitively count unique HTTP response status messages
cut -d '"' -f 3 /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -ic