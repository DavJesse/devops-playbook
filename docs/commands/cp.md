# cp

## Overview
Copies files and directories across local filesystems and mounted storage volumes. System administrators and DevOps engineers use it to replicate configuration states, create pre-modification backups, and stage deployment artifacts while controlling metadata and symlink preservation.

## Common Pitfalls & Edge Cases
- **Metadata stripping by default:** Plain `cp file dest` resets file ownership to the current invoking user, strips access control lists (ACLs) or extended attributes, and updates timestamps to the current execution time. In infrastructure operations, this can break service permissions on target hosts unless `-p` or `-a` is explicitly supplied.
- **Symlink dereferencing surprises:** Without `-d` or `-a`, copying a symbolic link follows (dereferences) the link and copies the target content as a regular file rather than duplicating the symlink itself. This can duplicate gigabytes of referenced storage or cause endless recursion loops.
- **Trailing slash directory nesting:** Running `cp -r src/ dest/` behaves differently depending on whether `dest/` already exists. If `dest/` exists, `cp` nests the source inside it (`dest/src/`). If it does not exist, it copies the contents into `dest/`. Use trailing slash dots (`src/.`) or the `-T` flag to enforce deterministic target behavior.
- **Interactive alias bypass in scripts:** Many interactive distributions alias `cp` to `cp -i` (prompt before overwrite). Automated shell scripts and provisioning loops can hang indefinitely waiting for standard input. Use `\cp`, `/bin/cp`, or `-f` to guarantee non-interactive execution.

## Essential Flags
| Flag | Name | Function |
|---|---|---|
| `-a` | `--archive` | Preserves all file attributes (permissions, timestamps, ownership, symlinks) recursively; equivalent to `-dR --preserve=all`. |
| `-r` / `-R` | `--recursive` | Copies directories and their nested contents recursively. |
| `-p` | `--preserve[=ATTR]` | Preserves specified attributes (default: `mode,ownership,timestamps`). |
| `-u` | `--update` | Copies only when the source file is newer than the destination or when the destination is missing. |
| `-n` | `--no-clobber` | Does not overwrite an existing file (silently overrides any preceding `-i`). |
| `-b` | `--backup[=CONTROL]` | Creates a backup copy of each existing destination file before overwriting. |
| `-T` | `--no-target-directory` | Treats the destination strictly as a normal file, preventing unwanted directory nesting. |

## Production Use Cases
Site Reliability Engineers rely on `cp` with strict attribute retention (`-a`) to snapshot sensitive configuration directories (such as `/etc/nginx/` or `/etc/systemd/`) prior to running migrations or package upgrades, and to synchronize staged application builds into release directories using timestamp-based updates (`-u`).

## Production Examples

```bash
# Snapshot a live system configuration directory while preserving ownership, permissions, and symlinks
cp -a /etc/nginx /etc/nginx.bak.$(date +%F)

# Safely copy files without overwriting existing production target configurations
cp -n ./configs/*.env /etc/app/conf.d/

# Sync only newer assets into a deployment staging folder
cp -ru ./build/static/* /var/www/html/static/

# Force copy without hanging on shell interactive aliases (prompt bypass)
\cp -f ./entrypoint.sh /usr/local/bin/entrypoint.sh

# Create numbered backups of overwritten target files automatically during deployment
cp --backup=numbered ./updated-service.conf /etc/systemd/system/app.service