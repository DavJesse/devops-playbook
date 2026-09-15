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