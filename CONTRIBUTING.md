# Contributing to devops-playbook

Thank you for contributing! This repository serves as a clear, scannable, and production-tested Linux operational reference. 

To maintain high technical accuracy and stylistic consistency, all contributions must follow the guidelines below.

---

## Code of Conduct & Standards

All documentation follows the [Google Technical Writing Guidelines](https://developers.google.com/tech-writing):
* **Active voice:** State actions directly (e.g., "Run `find` to locate stale files", not "Stale files can be located with `find`").
* **Present tense:** Describe what the command does, not what it will do.
* **Scannability:** Prefer tables and bulleted lists over dense narrative blocks.
* **Concrete over generic:** Avoid vague phrases like "a popular utility." Focus on operational behavior, default values, and concrete production scenarios.

---

## Contribution Workflow

### 1. Issue First
Before drafting a new command or overhauling an existing guide, search existing issues or open a new issue proposing the addition. This prevents duplicate work.

### 2. Branching & Commit Conventions

Always link branches and commits to an active issue:

* **Branch format:** `<type>/<issue-number>-<short-description>`
  * `docs/12-add-grep-command`
  * `fix/18-find-byte-syntax`
  * `refactor/24-tail-examples`

* **Commit format:** Use Conventional Commits with the issue number referenced at the end:
  * `docs: add grep command (#12)`
  * `fix: correct size unit flag in find guide (#18)`
  * `refactor: update tail production examples (#24)`

### 3. Template Compliance
Every new command added under `docs/commands/` **must** strictly adhere to the blueprint defined in [`docs/templates/command-template.md`](docs/templates/command-template.md). Do not alter or omit template section headers.

### 4. Update the Index
Whenever you add a new command:
1. Create `docs/commands/<command-name>.md`.
2. Add a corresponding entry to the table in root [`README.md`](README.md) linking to your file.

### 5. Verified Examples
Every command and flag listed in a PR must be tested on a Linux environment (e.g., Ubuntu, Debian, or WSL) before submission. Untested or speculative one-liners are not accepted.

---

## Submitting a Pull Request
1. Push your branch to your fork.
2. Open a Pull Request against `main`.
3. Provide a concise summary in the PR description:
   * **Linked Issue:** Include `Closes #<issue_number>` (or `Fixes #<issue_number>`) so GitHub automatically closes the issue once merged.
   * **Changes:** State which command is being added or modified.
   * **Production Use Case:** Briefly outline the real-world operational scenario covered.
   * **Verification:** Confirm that the document strictly adheres to `command-template.md` and that all commands have been tested.