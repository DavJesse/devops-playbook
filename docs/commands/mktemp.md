# mktemp

## Overview
Generates uniquely named temporary files or directories safely to prevent symlink race conditions and naming collisions. Scripts and automation pipelines use it to create isolated, non-predictable scratch spaces in `/tmp` or custom storage paths.

## Common Pitfalls & Edge Cases
- **Uncollected garbage on exit:** `mktemp` only allocates the resource; it does not clean it up. Unhandled script failures leave orphaned files filling `/tmp`. Always bind cleanup to a `trap ... EXIT` signal handler.
- **Template string requirements:** Custom templates must contain at least three consecutive trailing `X` characters (GNU standard recommends six: `XXXXXX`). Placing characters after the trailing `X` sequence causes template validation errors unless paired with `--suffix`.
- **Dry-run security vulnerability (`-u`):** The unsafe dry-run flag (`-u`) outputs a prospective file name without creating it. This introduces a Time-of-Check to Time-of-Use (`TOCTOU`) race condition where another process can claim the path before your script creates it.
- **Root filesystem exhaustion:** Defaulting to `/tmp` uses the root partition or a bounded `tmpfs` RAM mount. Writing large artifacts without overriding the directory path using `-p` risks exhausting host memory or root disk capacity.

## Essential Flags
| Flag | Name | Function |
|---|---|---|
| `-d` | `--directory` | Creates a temporary directory instead of a regular file. |
| `-p DIR` | `--tmpdir[=DIR]` | Interprets the template relative to `DIR`, falling back to `$TMPDIR` or `/tmp`. |
| `-q` | `--quiet` | Suppresses error diagnostics if file or directory creation fails. |
| `-u` | `--dry-run` | Prints a unique name without creating the file (unsafe; introduces TOCTOU flaws). |
| `--suffix=SUF` | `--suffix=SUF` | Appends `SUF` to the template; allows extensions like `.tar.gz` after `XXXXXX`. |

## Production Use Cases
DevOps engineers reach for `mktemp` inside CI/CD agent scripts, backup routines, and database maintenance tasks to stage uncompressed data payloads, generate dynamic configuration files, or clone git subtrees without clobbering concurrent jobs running on the same runner host.

## Production Examples

```bash
# Create an isolated temporary directory with an automated cleanup trap on script termination
SCRATCH_DIR=$(mktemp -d -t deploy-XXXXXX)
trap 'rm -rf "$SCRATCH_DIR"' EXIT

# Create a secure temporary file with a specific extension for staging configs
CONFIG_BUFFER=$(mktemp --suffix=.yaml config-XXXXXX)
trap 'rm -f "$CONFIG_BUFFER"' EXIT

# Direct temporary artifact creation to a dedicated high-capacity mount instead of /tmp
BIG_SCRATCH=$(mktemp -d -p /mnt/storage/tmp data-stage-XXXXXX)
trap 'rm -rf "$BIG_SCRATCH"' EXIT