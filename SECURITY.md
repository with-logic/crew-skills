# Security Policy

## Reporting a Vulnerability

Please report security issues privately by emailing security@logic.inc.

Do not open a public GitHub issue for a suspected vulnerability. Include as
much of the following as you can:

- The skill (directory name) and commit involved.
- The agent coder, OS, and `crew` version where you observed the issue.
- The command, prompt, or workflow that exposes the issue.
- Reproduction steps, proof of concept, or relevant logs.
- Impact: what an attacker could read, write, execute, or cause an agent or
  user to trust.

We will review the report, follow up if we need more detail, and coordinate a
fix and disclosure timeline.

## Scope

`crew-skills` is the default `core` tap shipped with
[`crew`](https://github.com/with-logic/crew). Every skill here ships verbatim
to every user who installs it, and the instructions are read directly by
agent coders. Security reports are especially useful for:

- A skill that instructs an agent to execute untrusted shell commands, follow
  symlinks out of the working tree, exfiltrate environment variables, or
  write outside the project.
- A skill that contains secrets, private data, internal hostnames, or
  credentials in examples, fixtures, or reference files.
- Prompt-injection or jailbreak payloads embedded in skill content.
- A skill name, description, or frontmatter shape designed to impersonate or
  shadow another skill in `crew search` / `crew install`.
- Supply-chain issues in the GitHub Actions workflows under
  `.github/workflows/` (e.g. an unpinned action, an injection sink in a
  workflow input).

Vulnerabilities in the `crew` CLI itself (installer, tap resolution, update
flow) belong in the [`crew`](https://github.com/with-logic/crew) repo's
security policy.

Normal bugs, feature requests, copy fixes, and support questions can use
GitHub issues.

## Supported Versions

This is a rolling content repository. Fixes land on `main` and are picked up
the next time a user runs `crew search` or `crew install`. There are no
versioned branches to back-port to.
