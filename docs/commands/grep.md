# grep

## Overview
`grep` searches input files or streams line by line for lines that match a specified pattern or regular expression. In production engineering, it serves as the foundational text-filtering engine for log investigation, pipeline data extraction, and configuration audits.

## Common Pitfalls & Edge Cases

* **Unquoted Patterns Breaking on Shell Metacharacters:** Passing raw regular expressions containing characters like `*`, `?`, `[`, or `$` without quotes causes the shell to expand them as file paths before passing them to `grep`. Always enclose patterns in single quotes: `grep 'pattern.*' file.txt`.
* **Standard Regex vs. Extended Regex (`-E`):** By default, basic regular expressions (BRE) treat grouping symbols (`(`, `)`) and alternation (`|`) as literal characters unless escaped with backslashes (`\(` or `\|`). Use `-E` (ERE) or `egrep` to enable standard modern regex syntax without backslash escaping.
* **Pipeline Buffering Lag in Follow Mode:** When piping output from streaming commands (`tail -f`) through `grep` into another processor (such as `awk` or a webhook), standard I/O block buffering delays terminal output. Pass `--line-buffered` to force `grep` to flush matches immediately.
* **Binary File Match False Positives:** By default, `grep` alerts with `Binary file matches` when matching against binary payloads, core dumps, or compressed streams, halting clean stdout parsing. Use `-I` to ignore binary files completely or `-a` to force `grep` to process them as text.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-i` | Ignore Case | Disables case sensitivity during pattern evaluation. |
| `-v` | Invert Match | Inverts the match condition to select non-matching lines. |
| `-r` / `-R` | Recursive Search | Searches subdirectories recursively (`-R` dereferences symlinks). |
| `-E` | Extended Regex | Interprets the pattern as an extended regular expression (ERE). |
| `-n` | Line Number | Prefixes each matching line with its 1-based line number in the source file. |
| `-c` | Count Matches | Suppresses regular line output and prints only the count of matched lines. |
| `-l` | Files with Matches | Prints only the names of files containing matches instead of matching lines. |
| `-o` | Only Matching | Prints only the exact substring matching the pattern instead of the full line. |
| `--line-buffered` | Line Buffered | Flushes output immediately on each newline; essential for real-time pipeline monitoring. |

## Production Use Cases

* **Real-Time Error Triage During Incidents:** DevOps engineers filter live application log streams to isolate HTTP 5xx spikes, database deadlocks, and fatal exceptions across active workloads.
* **Configuration Audits Across Microservices:** Platform operators scan repository trees or container mount points to locate deprecated environment variables, outdated base images, or plaintext tokens.

## Production Examples

```bash
# Filter live Nginx logs for HTTP 500-level error responses with immediate flushing
tail -F /var/log/nginx/access.log | grep --line-buffered -E 'HTTP/1\.[01]" 5[0-9]{2}'

# Recursively scan application code for deprecated API endpoints, ignoring case and showing line numbers
grep -rni 'v1/auth/tokens' /var/www/app/src

# Isolate matching IP addresses only from an authentication failure log
grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' /var/log/auth.log

# Exclude commented configuration lines and blank lines to review active directives
grep -vE '^\s*(#|$)' /etc/postgresql/15/main/postgresql.conf