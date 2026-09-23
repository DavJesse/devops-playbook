# tar

## Overview
Archives multiple files and directories into a single stream or file (tape archive) while preserving filesystem metadata, permissions, and directory structures. Systems and DevOps engineers use it to package application builds, manage backup snapshots, and stream filesystem trees across networks.

## Common Pitfalls & Edge Cases
- **The Tar Bomb (lack of root container directory):** Extracting an archive created without a top-level parent folder scatters hundreds of files across your current working directory. Always inspect archive contents with `tar -tf <archive>` or enforce an explicit destination directory using `-C <target_dir>`.
- **Accidental flag ordering with `-f`:** The `-f` option requires the archive filename as its immediate next argument. Writing `tar -cvf archive.tar.gz ./src` works, but writing `tar -czvf ./src archive.tar.gz` treats `./src` as the output archive file, overwriting source directories or failing unpredictably.
- **Absolute path leading slash stripping:** GNU `tar` strips leading slashes (`/`) by default to prevent catastrophic overwrites of system files during extraction (e.g., `/etc/hosts` becomes `etc/hosts`). Forcing absolute paths with `-P` introduces severe security risks when extracting untrusted archives.
- **Symlink traversal versus preservation:** By default, `tar` archives symbolic links as links rather than copying the target contents. If an archive must bundle the dereferenced target files (e.g., when shipping self-contained deployment packages), pass `-h` (`--dereference`).

## Essential Flags
| Flag | Name | Function |
|---|---|---|
| `-c` | `--create` | Creates a new archive. |
| `-x` | `--extract` | Extracts files from an archive. |
| `-t` | `--list` | Lists the table of contents of an archive without extracting it. |
| `-z` | `--gzip` | Filters the archive through `gzip` compression (standard `.tar.gz` / `.tgz`). |
| `-j` | `--bzip2` | Filters the archive through `bzip2` compression (standard `.tar.bz2`). |
| `-J` | `--xz` | Filters the archive through `xz` compression (higher ratio, standard `.tar.xz`). |
| `-v` | `--verbose` | Verbosely lists files processed during archive creation or extraction. |
| `-f ARCHIVE` | `--file=ARCHIVE` | Specifies the target archive file name or device node (mandatory). |
| `-C DIR` | `--directory=DIR` | Changes directory to `DIR` before performing extraction or addition operations. |
| `--exclude=PAT` | `--exclude=PAT` | Excludes files matching the specified pattern from being added to the archive. |

## Production Use Cases
DevOps engineers rely on `tar` to package immutable build artifacts and container layers in CI/CD pipelines, execute high-speed local filesystem tree clones over SSH without intermediate disk storage, and create compressed snapshots of configuration directories with full POSIX permissions intact.

## Production Examples

```bash
# Package and compress a directory using gzip while excluding build and version control caches
tar -czvf release-v1.4.0.tar.gz --exclude='.git' --exclude='node_modules' ./app

# Safely extract an archive into a specific target directory rather than the current working path
tar -xzvf release-v1.4.0.tar.gz -C /var/www/staging/

# Preview an archive's contents before extracting to inspect the directory structure
tar -tzvf release-v1.4.0.tar.gz

# Stream and extract a directory tree across the network over SSH without saving a local tar file
tar -czf - /var/log/nginx | ssh deploy@backup-server.internal "tar -xzf - -C /mnt/backups/nginx/"

# Archive preserving exact file permissions, extended attributes, and SELinux contexts
tar --xattrs --acls -cpzf sys-backup.tar.gz /etc/