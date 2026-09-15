# base64

## Overview
`base64` encodes and decodes data between raw byte streams and printable ASCII representations. In cloud and DevOps environments, it packages binary assets, credentials, and configuration payloads for transport over text-based protocols like HTTP, JSON, and YAML.

## Common Pitfalls & Edge Cases

* **Accidental Newline Injection in Encoded Strings:** Running `echo "mysecret" | base64` includes the invisible trailing newline byte (`0x0A`) added by `echo`. When decoded by applications or APIs expecting exact credentials, this extra newline causes authentication failures. Always use `printf` or `echo -n` to strip trailing newlines: `printf '%s' "mysecret" | base64`.
* **Line Wrapping Breaking API Payloads (`-w`):** By default, GNU `base64` wraps output lines at 76 characters, inserting newline breaks. When injecting base64-encoded strings into single-line fields (such as HTTP `Authorization: Basic` headers or JSON properties), these newlines cause header parsing or JSON validation errors. Disable wrapping with `-w 0`.
* **Garbage or Whitespace Halting Decodes (`-i`):** Strict decoders crash or emit partial data when encountering unexpected whitespace, carriage returns (`\r`), or characters outside the standard Base64 alphabet. Use `-i` (`--ignore-garbage`) to safely discard non-alphabet characters during decoding.
* **Encoding Is Not Encryption:** Base64 provides zero cryptographic confidentiality or integrity; anyone with shell access can decode it instantly. Never treat Base64 encoding as an alternative to encryption for secrets at rest.

## Essential Flags

| Flag | Name | Function |
|---|---|---|
| `-d` / `--decode` | Decode Data | Decodes base64-encoded input back into raw standard output. |
| `-w` / `--wrap` | Wrap Columns | Wraps encoded lines after $N$ characters (default: 76). Use `-w 0` to disable wrapping entirely. |
| `-i` / `--ignore-garbage` | Ignore Garbage | Discards non-alphabet characters during decoding, preventing failures from rogue whitespace or formatting bytes. |

## Production Use Cases

* **Managing Kubernetes Secrets:** Platform engineers decode and inspect opaque secret manifests (`.data` fields) from Kubernetes clusters or encode credentials into single-line strings before committing manifests.
* **Cloud-Init User Data & Automation:** SREs encode shell scripts, SSH keys, or TLS certificates into base64 blobs for embedding inside cloud provisioning templates (AWS CloudFormation, Terraform, or GCP metadata).

## Production Examples

```bash
# Encode a credential cleanly without a trailing newline or line breaks
printf '%s' "db_super_password" | base64 -w 0

# Decode a Kubernetes secret data field directly from stdout
echo "bXlzZWNyZXRwYXNzd29yZA==" | base64 -d

# Encode an entire binary TLS certificate for embedding into a Terraform file
base64 -w 0 /etc/ssl/certs/app.crt > cert.base64

# Decode a dirty base64 payload while ignoring rogue line breaks and spaces
base64 -d -i payload_with_spaces.txt