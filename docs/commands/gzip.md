# gzip

## Overview
Compresses and decompresses single files using the DEFLATE algorithm, appending a `.gz` extension to target files by default. DevOps and infrastructure engineers rely on it to shrink rotated system logs, minimize static asset payloads for web delivery, and reduce bandwidth requirements across backup pipelines.

## Common Pitfalls & Edge Cases
- **Automatic source deletion:** By default, `gzip` deletes the original uncompressed file after creating the `.gz` archive. In production scripts, omitting the `-k` (`--keep`) flag can inadvertently destroy active configuration files or uncompressed assets.
- **Inability to archive directories:** Running `gzip -r directory/` does not bundle the folder into a single file. Instead, it recursively traverses the directory tree and compresses every individual file in place. Use `tar -czf` when bundling multiple files or entire folder hierarchies into a unified archive.
- **Pipeline stdout behavior:** By default, writing decompressed output using `gzip -d` expects a target file on disk. When consuming streams from stdin or sending output down UNIX pipes, explicitly pass `-c` (`--stdout`) to avoid errors or unexpected local file creation.
- **CPU vs ratio trade-offs at maximum compression (`-9`):** Compressing multi-gigabyte log dumps with `-9` yields marginal size savings over the default level (`-6`) while consuming significantly more CPU time and core cycles, risking CPU throttling on shared cloud instances.

## Essential Flags
| Flag | Name | Function |
|---|---|---|
| `-d` | `--decompress` | Decompresses the specified `.gz` file (equivalent to `gunzip`). |
| `-k` | `--keep` | Keeps (does not delete) the original input files during compression or decompression. |
| `-c` | `--stdout` | Writes output directly to standard output, leaving original files unchanged. |
| `-f` | `--force` | Forces compression or decompression even if links exist or target files already exist. |
| `-l` | `--list` | Displays compression ratio, uncompressed size, and compressed size for each archive. |
| `-r` | `--recursive` | Traverses directory structure recursively, compressing or decompressing every file individually. |
| `-1` to `-9` | `--fast` to `--best` | Sets the compression level (1 is fastest/least compression; 9 is slowest/best compression; default is 6). |

## Production Use Cases
Site Reliability Engineers use `gzip` to compress rotated access logs during scheduled cron or logrotate routines, serve pre-compressed static assets (`.js.gz`, `.css.gz`) on Nginx and Cloudflare CDN origin nodes, and stream compressed database dumps directly to remote object storage without writing giant uncompressed SQL dumps to disk.

## Production Examples

```bash
# Compress a large rotated log file while preserving the original source file
gzip -k /var/log/nginx/access.log.1

# Decompress a .gz payload while keeping the compressed archive intact
gzip -dk database_dump.sql.gz

# Stream a live MySQL database export directly through gzip to disk
mysqldump -u root -p production_db | gzip -c > /backups/db-$(date +%F).sql.gz

# Inspect the compression ratio and uncompressed footprint of an archive
gzip -l /backups/db-2026-09-23.sql.gz

# Decompress a gzipped stream directly into a downstream command pipeline
gzip -dc application_metrics.json.gz | grep "500 Internal Server Error"

# Pre-compress static web assets using maximum compression for production web serving
gzip -k -9 /var/www/html/assets/app.bundle.js