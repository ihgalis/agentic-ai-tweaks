---
name: script-security-review
author: ihgalis
description: Use whenever creating, rewriting, or materially changing scripts, shell commands, cron jobs, backup/restore tooling, deployment helpers, or automation. Forces a focused security review before the work is considered complete.
---

# Script Security Review

Use this skill before finishing any new or materially changed script or automation.

## Required Workflow

1. Identify trust boundaries: user arguments, environment variables, config files, filenames, archive contents, network input, command output, cron/systemd context, and inherited permissions.
2. Review dangerous behaviors: command injection, shell expansion, unsafe eval/source, globbing surprises, word splitting, path traversal, symlink/hardlink attacks, unsafe temp files, plaintext secret exposure, weak permissions, recursive deletion, archive extraction, network downloads, and privilege escalation.
3. Check operational safety: fail-closed behavior, cleanup on every exit path, dry-run/help behavior, logging without secrets, clear errors, dependency checks, idempotency, and rollback/recovery where practical.
4. Fix concrete issues before declaring the script done.
5. Reevaluate the changed code and report whether each mitigation succeeded.

## Shell/Bash Baseline

- Prefer arrays over string-built commands.
- Quote variable expansions unless intentional word splitting is required and explained.
- Use set -Eeuo pipefail for scripts where it fits the repo's style.
- Set restrictive permissions for secret-bearing files and staging dirs.
- Create temp files/dirs with mktemp; clean them with a trap.
- Validate paths before deletion or archive extraction; reject empty, root, home, or broad paths unless explicitly intended.
- Use -- before path operands when supported.
- Avoid eval, untrusted source, command strings passed through sh -c, and unpinned network execution.
- Do not log secrets, tokens, passphrases, or full sensitive paths unless Andre explicitly asks.

## Report Format

When the user asked for security analysis, lead with findings by severity:

- Critical/High/Medium/Low: issue, impact, and affected file/line.
- Fix: what changed.
- Recheck: why the mitigation now holds.

If no issue remains, say that clearly and list residual risks or assumptions.