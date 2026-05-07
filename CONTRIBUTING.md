# Contributing

Thanks for helping improve the default `crew` skills tap.

This repo is the `core` tap shipped with
[`crew`](https://github.com/with-logic/crew) — every skill here is
discoverable via `crew search` and installable via `crew install
<name>`. That gives every change a wide blast radius, so this guide
sets the bar.

## Before You Open a PR

- Search existing skills, issues, and PRs first. If a skill in the
  same neighborhood already exists, prefer extending it over adding
  a near-duplicate.
- Keep PRs small and focused. One new skill, or one edit to one
  skill, per PR is easiest to review.
- For security issues, do not open a public issue. Email
  security@logic.inc. See [SECURITY.md](./SECURITY.md).
- Do not include secrets, credentials, customer data, or internal
  hostnames in any file — examples, fixtures, references, anything.
  Every line ships verbatim to every user.

## Adding a Skill

1. Create a new directory at the repo root named after the skill.
   Lowercase, hyphenated, no spaces (e.g. `python-testing`). The
   directory name *is* the install identifier — pick one you're
   willing to keep stable.
2. Write `SKILL.md` following the
   [Agent Skills specification](https://agentskills.io/specification).
   Frontmatter floor:
   ```yaml
   ---
   name: python-testing
   description: Run and write Python tests the way this team does.
   ---
   ```
3. Write the `description` carefully. It's what `crew search`
   surfaces and what agent coders use to decide whether to invoke
   the skill. One concrete sentence about *what the skill does and
   when to use it* — not marketing copy, not a feature list. Vague
   descriptions pollute search and degrade routing.
4. Keep the skill self-contained. All supporting files (templates,
   reference docs, agent configs) live inside the skill's directory
   and are referenced from `SKILL.md`. No orphan files; no
   dangling references.
5. Match the voice of the existing skills: direct, plain,
   instruction-shaped. No marketing puffery.
6. Open a pull request.

## Editing an Existing Skill

- Match the surrounding tone and structure. If you're rewriting a
  skill, say so in the PR description.
- Renaming a skill (the directory name) is a breaking change for
  anyone who has installed it — call this out explicitly in the PR.
  Prefer additive changes.

## Repo Plumbing

Workflows, dependabot config, security policy, and review prompt
mirror the patterns in the main
[`crew`](https://github.com/with-logic/crew) repo on purpose.
Consistency across the two repos is a feature; if you're changing
plumbing here, check whether the same change should land there
(or vice-versa).

## Review Expectations

PRs get an automated Claude Code review on open
(see [.github/pr-review-prompt.md](./.github/pr-review-prompt.md))
plus a human review. Expect questions about:

- Whether the skill's `description` is concrete enough to route
  correctly.
- Whether the skill overlaps with something that already exists.
- Whether agent-executed instructions are safe (no untrusted shell,
  no symlink escape, no exfiltration paths).
- Whether the skill is self-contained and references resolve.
