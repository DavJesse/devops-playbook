# DevOps Playbook: Linux Reference & Operations

A structured operational reference for essential Linux commands, system behaviors, and production troubleshooting patterns.

## Command Index

| Command | Category | Core Purpose | Production Scenario | Reference |
|---|---|---|---|---|
| `find` | File Inspection | Recursive search by file metadata | Cleaning up stale Docker log volumes | [find.md](docs/commands/find.md) |
| `file` | Data Analysis | Magic-byte file type identification | Validating uploaded binary payloads | [file.md](docs/commands/file.md) |
| `head` | Data Sampling | Inspect file headers and initial bytes | Sampling large CSV/log schemas | [head.md](docs/commands/head.md) |
| `tail` | Observability | Stream or inspect terminal lines | Live log ingestion & pipeline debugging | [tail.md](docs/commands/tail.md) |
| `grep` | Text Processing | Filter streams and files by regex patterns | Live error triage and config auditing | [grep.md](docs/commands/grep.md) |
| `sort` | Text Processing | Reorder streams by lexicographical or numeric keys | Ranking top traffic consumers and log metrics | [sort.md](docs/commands/sort.md) |
| `uniq` | Text Processing | Filter or report repeated adjacent lines | Generating IP frequency tables and isolating anomaly records | [uniq.md](docs/commands/uniq.md) |
| `strings` | Data Analysis | Extract printable character sequences from binary files | Auditing compiled artifacts for embedded secrets and inspecting core dumps | [strings.md](docs/commands/strings.md) |
| `base64` | Data Serialization | Encode and decode binary data to printable ASCII | Managing Kubernetes Secret manifests and formatting Cloud-init userdata | [base64.md](docs/commands/base64.md) |
| `tr` | Text Processing | Translate, delete, or squeeze characters from stdin | Stripping Windows CRLF line endings and delimiter normalization | [tr.md](docs/commands/tr.md) |
| `mktemp` | System Automation | Create collision-safe temporary files or directories | Staging ephemeral CI/CD build artifacts with automated cleanup traps | [mktemp.md](docs/commands/mktemp.md) |
