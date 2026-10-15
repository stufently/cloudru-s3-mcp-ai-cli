# Cloud.ru S3 MCP Server & AI CLI

A Go MCP server, CLI and installable agent skill for Cloud.ru S3 Object Storage. Use Claude Code, Cursor and Codex to inspect objects, recover backups and work with scoped bucket prefixes. Evolution, s3.cloud.ru, ru-central-1; not an Advanced profile. Read-only MCP access is the default. Independent permission levels control writes; destructive operations use the configured approval policy.

[Русское руководство](README.md) · [Russian S3 MCP server directory](https://stufently.github.io/s3-mcp-ai-cli-kit/en/)

## Features

Streaming object CRUD, copy, multipart and presigned URLs follow the profile. Versions, tags, CORS, policy and Object Lock tools require their capabilities. Independent permissions, bounded analytics, diagnostics and installers preserve the shared safety contract.

## Quick start

This profile targets **Evolution Object Storage**. Supply the complete `<tenant_id>:<key_id>` as the access key ID to `auth set`; signing region is `ru-central-1`, endpoint `https://s3.cloud.ru`. Advanced OBS uses a separate API contract.

Use a platform binary from [Releases](https://github.com/stufently/cloudru-s3-mcp-ai-cli/releases/latest) (v0.1.0 is out; also listed in the MCP Registry and Smithery), or build with Go 1.27.2:

```bash
bash scripts/go.sh go build -o cloudru-s3 ./cmd/cloudru-s3
./cloudru-s3 version
./cloudru-s3 auth set
./cloudru-s3 auth status
./cloudru-s3 ls --json
```

auth set reads the key ID and secret without terminal echo; non-TTY input supplies two lines. It atomically creates ~/.config/cloudru-s3/credentials with mode 0600; broader permissions fail. Do not pass secrets in argv.

Resolution: flags > env > file. Variables: CLOUDRU_S3_ACCESS_KEY_ID, CLOUDRU_S3_SECRET_ACCESS_KEY, CLOUDRU_S3_POOL (ru-central-1), CLOUDRU_S3_ENDPOINT. HTTP requires CLOUDRU_S3_ALLOW_INSECURE=1.

Image: ghcr.io/stufently/cloudru-s3-mcp-ai-cli:0.1.0. Build locally with docker build -t cloudru-s3 .; the scratch image runs MCP as uid 65532 with CA certificates. Supply a private credentials environment/file setup.

## Connect to Claude Code, Cursor, Codex

[![Add cloudru-s3 MCP server to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=cloudru-s3&config=eyJjb21tYW5kIjoiZG9ja2VyIiwiYXJncyI6WyJydW4iLCItaSIsIi0tcm0iLCItZSIsIkNMT1VEUlVfUzNfQUNDRVNTX0tFWV9JRCIsIi1lIiwiQ0xPVURSVV9TM19TRUNSRVRfQUNDRVNTX0tFWSIsIi1lIiwiQ0xPVURSVV9TM19QT09MIiwiZ2hjci5pby9zdHVmZW50bHkvY2xvdWRydS1zMy1tY3AtYWktY2xpOjAuMS4wIl0sImVudiI6eyJDTE9VRFJVX1MzX0FDQ0VTU19LRVlfSUQiOiIke2VudjpDTE9VRFJVX1MzX0FDQ0VTU19LRVlfSUR9IiwiQ0xPVURSVV9TM19TRUNSRVRfQUNDRVNTX0tFWSI6IiR7ZW52OkNMT1VEUlVfUzNfU0VDUkVUX0FDQ0VTU19LRVl9IiwiQ0xPVURSVV9TM19QT09MIjoiJHtlbnY6Q0xPVURSVV9TM19QT09MfSJ9fQ%3D%3D)

The button installs the MCP server from the Docker image `ghcr.io/stufently/cloudru-s3-mcp-ai-cli:0.1.0`, read-only. Keys stay out of the config: Cursor fills `${env:…}` from the environment, so set `CLOUDRU_S3_ACCESS_KEY_ID` and `CLOUDRU_S3_SECRET_ACCESS_KEY` where Cursor starts, and `CLOUDRU_S3_POOL` when the bucket is not in the default pool (empty means the default). The same block works for Windsurf (`mcp_config.json`) and by hand in `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "cloudru-s3": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "CLOUDRU_S3_ACCESS_KEY_ID",
        "-e",
        "CLOUDRU_S3_SECRET_ACCESS_KEY",
        "-e",
        "CLOUDRU_S3_POOL",
        "ghcr.io/stufently/cloudru-s3-mcp-ai-cli:0.1.0"
      ],
      "env": {
        "CLOUDRU_S3_ACCESS_KEY_ID": "${env:CLOUDRU_S3_ACCESS_KEY_ID}",
        "CLOUDRU_S3_SECRET_ACCESS_KEY": "${env:CLOUDRU_S3_SECRET_ACCESS_KEY}",
        "CLOUDRU_S3_POOL": "${env:CLOUDRU_S3_POOL}"
      }
    }
  }
}
```

```bash
cloudru-s3 mcp install --client cursor --dry-run
cloudru-s3 mcp install --client cursor
cloudru-s3 mcp install --client claude-desktop
cloudru-s3 mcp install --client codex
cloudru-s3 mcp install --client claude-code --scope project
cloudru-s3 mcp install --client claude-code --scope user
```

The final command only prints claude mcp add. Executable paths are absolute; secrets stay out of client configuration. Cursor uses ~/.cursor/mcp.json, Claude Code project scope ./.mcp.json, Desktop its OS-specific directory. JSON updates preserve foreign keys, create .bak and are idempotent.

Codex uses ~/.codex/config.toml. Existing tables require --force and must have generated # >>> cloudru-s3 (managed) / # <<< cloudru-s3 (managed) markers. Unmanaged tables are refused even with --force; other TOML bytes stay intact.

cloudru-s3 mcp --allow-write --allow-delete --allow-admin or CLOUDRU_S3_ALLOW=write,delete,admin enables independent levels. These flags also work for mcp install. File tools require CLOUDRU_S3_FILE_ROOT.

## MCP tools

| Level | Tools |
|---|---|
| read | s3_list_buckets, s3_list_objects, s3_head_object, s3_get_object, s3_preview_object, s3_presign_get, s3_list_object_versions, s3_list_multipart_uploads, s3_list_parts, s3_get_bucket_config, s3_largest_objects, s3_prefix_usage, s3_unfinished_uploads, s3_get_object_tagging |
| read + FILE_ROOT | s3_download_file |
| write | s3_put_object, s3_create_folder, s3_copy_object, s3_presign_put, s3_multipart_create, s3_multipart_presign_part, s3_multipart_complete, s3_put_object_tagging |
| write + FILE_ROOT | s3_upload_file |
| delete | s3_delete_object, s3_delete_objects, s3_multipart_abort |
| admin | s3_create_bucket, s3_put_bucket_versioning, s3_put_bucket_cors, s3_put_bucket_policy, s3_delete_bucket_policy, s3_put_object_lock_configuration |
| admin + delete | s3_delete_bucket |

Resources: `cloudru-s3://config`, `cloudru-s3://capabilities`, `cloudru-s3://pools`. Disabled capabilities remove tools.

Lists return at most 1000 items plus continuation tokens; versions/multipart have secondary markers. Inline get/put is limited to 262144 bytes; preview to 65536. Use presigned URLs or FILE_ROOT transfers for larger objects. Responses include structuredContent and JSON text; text over 256 KiB is summarized. Errors have code/message/retryable; the SDK adaptively retries 429/503.

Analytics scan max_scan entries (default 10000), marking partial results and tokens. Prefix aggregates page via next_result_offset. They are not billing estimates.

## Use case: Evolution Object Storage access diagnostics

Start an Evolution migration or restore by checking access and scanning a bounded prefix. This profile is not a configuration for another Cloud.ru S3 product with a different endpoint or region:

```bash
cloudru-s3 doctor --json
cloudru-s3 --pool ru-central-1 ls backups/daily/ --json
cloudru-s3 cp s3://backups/daily/archive.tar ./restored-cloudru.tar
```

Examples assume an existing `backups` bucket and `daily/archive.tar` object; replace them with your own names. Cloud compatibility still requires [live verification](TASKS.md).

## CLI

```bash
cloudru-s3 mb backups
cloudru-s3 cp ./archive.tar s3://backups/daily/archive.tar --part-size 5242880 --overwrite --yes
cloudru-s3 ls backups/daily/ --max-keys 100 --json
cloudru-s3 stat backups/daily/archive.tar
cloudru-s3 cp s3://backups/daily/archive.tar ./restored.tar
cloudru-s3 cp ./directory s3://backups/tree/ --recursive --overwrite --yes
cloudru-s3 cp s3://backups/tree/ ./restored-directory --recursive
cloudru-s3 cp s3://backups/daily/archive.tar s3://backups/copy.tar --overwrite --yes
cloudru-s3 cat backups/daily/notes.txt
cloudru-s3 versions backups/daily/ --json
cloudru-s3 du backups/daily/ --max-scan 10000 --json
cloudru-s3 presign backups/daily/archive.tar --expires 3600
cloudru-s3 rm backups/copy.tar --yes
cloudru-s3 rb empty-bucket --yes
cloudru-s3 doctor --json
cloudru-s3 compat --markdown
```

cp uses ordinary local paths and s3://bucket/key or cloudru-s3://bucket/key URIs. Large uploads use manager/multipart with configurable part size. --recursive copies directories/prefixes; overwrites require --overwrite and approval. Downloads are atomic. rm/rb/mv without --yes exit 2 on non-TTY input; terminals prompt. --json is global; errors use stderr and nonzero exits.

doctor checks credential source/permissions, DNS/TLS, Date-header clock skew and ListBuckets. Skew >5 minutes warns; >15 minutes fails.

## Agent skill

```bash
cloudru-s3 skill install
cloudru-s3 skill install --target ./agent-skills/cloudru-s3
```

Default: ~/.claude/skills/cloudru-s3. Idempotently installs SKILL.md and six references: backup/restore, sharing, least privilege, cost hygiene, migration and incidents. Triggers include cloudru s3, presigned and Russian bucket/upload/backup requests.

## Security

With `conditional_writes:false`, default writes refuse before S3 requests because HEAD alone is not atomic. An approved write requires CLI `--overwrite --yes` or MCP `overwrite:true, confirm:true` under the approval policy. With `conditional_delete:false`, `mv` and `s3_move_object` are disabled.



Read is the MCP default. Missing capabilities/permissions remove tools. CLOUDRU_S3_DESTRUCTIVE_POLICY=confirm uses client form elicitation when available, otherwise confirm:true after human approval. elicit requires elicitation and cannot be bypassed with confirm:true. block always refuses; allow proceeds.

Gates precede all S3 requests for deletion, move, signed PUT, policy/Object Lock, versioning suspension and overwrite. With conditional_writes:true, put/copy/move/multipart complete/upload file use overwrite:false with target HEAD and If-None-Match: *; otherwise default writes refuse. overwrite:true uses the same gate.

CLOUDRU_S3_ALLOWED_BUCKETS and CLOUDRU_S3_ALLOWED_PREFIXES take comma-separated buckets and bucket/prefix/ entries. Prefixes respect segment boundaries and reject '..'. Partial bucket scope denies bucket administration and broad listings even when the bucket is also listed in ALLOWED_BUCKETS. Copy/move checks both sides.

FILE_ROOT MCP tools use os.Root and relative paths only; absolute paths, '..' and escaping symlinks fail. Every tool call emits one JSON stderr audit record; CLOUDRU_S3_AUDIT_FILE additionally appends to a 0600 file. Secrets and X-Amz-Signature/Credential/Security-Token are redacted from logs/errors. Full signed URLs are operation results only. Keep underlying S3 key rights minimal too.

s3_download_file publishes downloads without replacing existing local files by default, including files created during the download. To replace a file, pass overwrite:true; the destructive policy applies, using form confirmation or confirm:true after human approval.

## Cloud.ru pools and storage classes

See [compatibility](docs/compat.md) for pools, endpoints and capabilities, and [official-source research](docs/research/cloudru-s3.md) for classes, pricing, limits and errors. Default signing pool: `ru-central-1`. The profile remains `verified:false`: an emulator cannot establish live-provider support. Unverified facts are marked **[предположение]**. Profile prices are not a billing calculator.

Lifecycle, ACL, bucket tagging and retention may be cloud features, but this binary does not expose management tools for them; the profile records S3 API compatibility.

## FAQ

**Is this MCP server for Cloud.ru Evolution or Advanced?** It targets **Evolution Object Storage**, using `https://s3.cloud.ru` and `ru-central-1`. Do not reuse those settings for Advanced; [research and sources](docs/research/cloudru-s3.md) cover Evolution.

**How do I check Cloud.ru S3 credentials and connectivity?** `cloudru-s3 auth status` checks local settings. `doctor --json` also checks DNS/TLS, server clock and ListBuckets; live operations require your own valid key.

**Is SINGLE a Multi-AZ storage class?** No. The profile lists `SINGLE` separately. A pool-level Multi-AZ flag does not imply identical redundancy for every class; see the [research](docs/research/cloudru-s3.md).

**What is an MCP server?** A program exposes agent tools/resources through Model Context Protocol; this server uses stdio.

**How do I connect Claude Code?** Save keys with auth set and use project install or the printed user command.

**Is agent S3 access safe?** Restrict keys, scope and permissions; retain confirm/block and deliberately approve destructive effects.

**How does it differ from aws-cli/rclone?** It combines MCP, gates, audit, analytics, skill and CLI; those tools remain independently useful for transfer/automation.

**Which features are available?** Read capabilities and [compat](docs/compat.md). Registration follows the profile; this version does not configure lifecycle, ACL, bucket tagging or retention.


## Related S3 MCP servers

[Browse the full provider directory](https://stufently.github.io/s3-mcp-ai-cli-kit/en/).

| Provider | Repository | Use case |
|---|---|---|
| Selectel | [selectel-s3-mcp-ai-cli](https://github.com/stufently/selectel-s3-mcp-ai-cli) | Pool selection and backup recovery |
| Yandex Cloud | [yandex-s3-mcp-ai-cli](https://github.com/stufently/yandex-s3-mcp-ai-cli) | Object inventory and backup version inspection |
| VK Cloud | [vkcloud-s3-mcp-ai-cli](https://github.com/stufently/vkcloud-s3-mcp-ai-cli) | Hotbox/Icebox inspection and regional access |
| Cloud.ru | [cloudru-s3-mcp-ai-cli](https://github.com/stufently/cloudru-s3-mcp-ai-cli) | Evolution Object Storage access diagnostics |
| Timeweb Cloud | [timeweb-s3-mcp-ai-cli](https://github.com/stufently/timeweb-s3-mcp-ai-cli) | Archive recovery and bounded prefix usage |
| MWS | [mws-s3-mcp-ai-cli](https://github.com/stufently/mws-s3-mcp-ai-cli) | HMAC access checks and short-lived archive URLs |
| Beget | [beget-s3-mcp-ai-cli](https://github.com/stufently/beget-s3-mcp-ai-cli) | Browse and restore files from an existing bucket |
| Shared Go kit | [s3-mcp-ai-cli-kit](https://github.com/stufently/s3-mcp-ai-cli-kit) | Shared MCP, CLI, safety and CI |

## License

[MIT](LICENSE). [Facts and assumptions](docs/research/cloudru-s3.md). Docker checks: bash scripts/go.sh go test -race ./..., bash scripts/conformance.sh, bash scripts/mcp_smoke.sh, bash scripts/mutation_gate.sh, bash scripts/check_docs.sh. Conformance uses versitygw v1.8.0 and fake keys.

Shared MCP/CLI behavior, safety and checks come from [s3-mcp-ai-cli-kit v0.1.0](https://github.com/stufently/s3-mcp-ai-cli-kit/tree/v0.1.0); this repository owns its embedded profile, thin main and Cloud.ru documentation.
