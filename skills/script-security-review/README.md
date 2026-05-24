# Script Security Review Skill

This skill is a focused safety gate for scripts and automation. Use it before marking a new or materially changed script, shell command, cron job, backup or restore tool, deployment helper, or other automation complete.

The goal is practical script safety: identify the places where untrusted input, filesystem behavior, credentials, permissions, and cleanup can break assumptions, then fix concrete risks before the work is declared done.

## What The Skill Does

The skill forces a short review loop:

1. Identify trust boundaries.
2. Review dangerous behaviors.
3. Check operational safety.
4. Fix concrete issues.
5. Reevaluate the changed code and report the result.

It is not a general code-style review. It is specifically aimed at mistakes that make scripts unsafe, brittle, hard to recover from, or likely to expose private data.

## Trust Boundaries It Checks

The review starts by locating inputs and execution context that can change script behavior:

- User arguments
- Environment variables
- Config files
- Filenames and paths
- Archive contents
- Network input
- Command output
- Cron or systemd execution context
- Inherited permissions

These are the places where assumptions usually fail. A script that is safe with a trusted literal path can be unsafe when the path comes from an argument, an archive, a config file, or a scheduled job running with a different working directory and environment.

## Unsafe Patterns It Looks For

The skill checks for common script security and reliability hazards:

- Command injection
- Shell expansion surprises
- Unsafe `eval` or `source`
- Globbing surprises
- Word splitting
- Path traversal
- Symlink and hardlink attacks
- Unsafe temporary files
- Plaintext secret exposure
- Weak permissions
- Recursive deletion mistakes
- Unsafe archive extraction
- Unpinned network downloads
- Privilege escalation paths

The important practice here is to review behavior, not just syntax. For example, quoting a variable may fix word splitting, but deletion logic still needs path validation so an empty, root, home, or overly broad path cannot be removed by accident.

## Operational Safety

The skill also checks whether the script behaves well when something goes wrong:

- Fails closed instead of continuing with partial or unsafe state
- Cleans up temporary files and staging directories on every exit path
- Provides useful dry-run or help behavior where practical
- Logs enough to diagnose failures without leaking secrets
- Reports clear errors
- Checks dependencies before doing work
- Is idempotent when repeated
- Has rollback or recovery behavior where practical

This is what makes the skill useful for backup, restore, deployment, cron, and host automation work. Those scripts often run unattended, handle sensitive data, or affect important services, so cleanup and failure behavior matter as much as the happy path.

## Shell And Bash Baseline

For shell scripts, the skill encourages these defaults when they fit the surrounding project:

- Prefer arrays over string-built commands.
- Quote variable expansions unless intentional word splitting is required and explained.
- Use `set -Eeuo pipefail` when it matches the script style.
- Use restrictive permissions for secret-bearing files and staging directories.
- Create temporary files and directories with `mktemp`.
- Clean temporary resources with a `trap`.
- Validate paths before deletion or archive extraction.
- Use `--` before path operands when supported.
- Avoid `eval`, untrusted `source`, command strings passed through `sh -c`, and unpinned network execution.
- Do not log secrets, tokens, passphrases, or full sensitive paths unless Andre explicitly asks.

These practices reduce the blast radius of normal script mistakes: bad quoting, unexpected filenames, hostile archive entries, missing dependencies, interrupted runs, and accidental exposure of private values.

## How To Use It Well

Use the skill near the end of script work, after the first implementation exists but before reporting that the task is complete.

For small changes, the review can be brief: identify the affected input paths, check the risky operations, fix anything concrete, and state the recheck result.

For scripts that delete files, extract archives, download content, handle secrets, run as root, or run from cron/systemd, the review should be stricter. Those scripts need explicit path validation, safe temporary directories, cleanup traps, secret-safe logging, and clear failure behavior.

## Report Shape

When the user asks for a security analysis, lead with findings by severity:

- Critical, High, Medium, or Low issue
- Impact
- Affected file and line
- Fix
- Recheck result

If no issue remains, say that clearly and list any residual risks or assumptions.

When the skill is used as a final gate during normal implementation, keep the report short: note the concrete mitigations checked and whether anything remains unresolved.

## What Good Looks Like

A script reviewed with this skill should be able to answer these questions:

- What input can influence commands, paths, permissions, archives, or network access?
- What happens if a path is empty, points at home/root, contains spaces, starts with a dash, or crosses a symlink?
- What happens if the script is interrupted halfway through?
- Are temporary plaintext files removed on every exit path?
- Are secrets excluded from logs and command output?
- Does the script fail before making changes when dependencies or required config are missing?
- Can it be rerun safely after a partial failure?

If those answers are clear, the script is usually much safer in practice.
