# mv

## Overview
Moves or renames files and directories across directories and filesystems. In production environments, it provides atomic renaming within a single filesystem and executes cross-device data transfers when migrating across mounts.

## Common Pitfalls & Edge Cases
- **Cross-filesystem non-atomicity:** Moving an item within the same filesystem simply updates the directory pointer (inode reference) instantaneously and atomically. Moving an item across mount boundaries (e.g., from `/tmp` to `/mnt/storage` or across Docker volume mounts) forces an unbuffered copy-then-delete sequence that is not atomic and can leave partial files on network or storage errors.
- **Silent destination nesting:** If the target directory already exists, running `mv source target` moves `source` inside `target/` instead of renaming it. If `target` does not exist, it renames `source` to `target`. To prevent unexpected nesting inside scripts, use `-T` (`--no-target-directory`).
- **Silent clobbering of production assets:** By default, GNU `mv` silently overwrites an existing destination file if write permissions allow. In automation scripts, use `-n` (`--no-clobber`) or `--backup` to prevent unintentional data loss.
- **Symlink destination confusion:** When the destination is a symlink pointing to a directory, moving an item to that link moves the item into the directory the symlink points to, rather than replacing the symlink.

## Essential Flags
| Flag | Name | Function |
|---|---|---|
| `-T` | `--no-target-directory` | Treats the destination strictly as a normal file or directory path, preventing source nesting if destination exists. |
| `-n` | `--no-clobber` | Does not overwrite an existing destination file (silently overrides any preceding `-i`). |
| `-i` | `--interactive` | Prompts for confirmation before overwriting an existing destination file. |
| `-u` | `--update` | Moves only when the source file is newer than the destination or when the destination is missing. |
| `-b` | `--backup[=CONTROL]` | Creates a backup copy of each existing destination file before overwriting it. |
| `-f` | `--force` | Does not prompt before overwriting destination; overrides preceding `-i`. |

## Production Use Cases
DevOps engineers rely on `mv` to perform zero-downtime application deployments using atomic symlink cutovers on the same mount, rotate active service log files before triggering a daemon reload, and stage large build outputs out of RAM-backed `/tmp` mounts onto persistent storage.

## Production Examples

```bash
# Rename a release directory deterministically without risk of nesting if target exists
mv -T ./releases/v2.1.0 /var/www/current_release

# Atomically cut over a symbolic link to point to a new release on the same filesystem
ln -sfn /var/www/releases/v2.1.0 /var/www/current_next
mv -Tf /var/www/current_next /var/www/current

# Rotate an active application log file without prompting
mv -f /var/log/app/access.log /var/log/app/access.log.1

# Move files to a target directory, backing up any conflicting files with numbered suffixes
mv --backup=numbered ./configs/*.conf /etc/app/conf.d/

# Protect existing configs from being overwritten during asset staging
mv -n ./staged-envs/.env.production /etc/app/.env