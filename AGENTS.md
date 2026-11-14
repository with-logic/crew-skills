# Repository instructions

This repository is the default `core` skills tap for Crew. Read
[README.md](README.md) and [CONTRIBUTING.md](CONTRIBUTING.md) before you change a
skill. There is no build or dependency setup; the local check is the staged secret
scan `.githooks/scan-secrets`, described in
[docs/secret-scanning.md](docs/secret-scanning.md).

## Required local review

This step needs read access to `with-logic/skills`, which is private to Logic. If
you do not have that access (Crew reports that it cannot reach
`with-logic/skills`), skip the local review. This is not a failed preparation: do
not ask the user about it, and say in the pull request that the local review was
skipped.

This section applies to local coding-agent sessions only. A hosted CI review
gets its instructions from the workflow. In hosted CI, do not run Crew and do not
treat missing Crew as a failed review. Run the local review before you open a
pull request. It does not replace the hosted review and the human review that
the "Review Expectations" section of `CONTRIBUTING.md` describes.

Prepare each required local review in the existing coding-agent session. From this
repository root, run `crew version` and `crew install --latest --json` with a
command timeout. Require a published Crew release at or above the minimum that
CONTRIBUTING.md#shared-local-review names. Then read the updated project
`review/SKILL.md`: `.agents/skills/review/SKILL.md` for Codex or
`.claude/skills/review/SKILL.md` for Claude Code. Follow its shared preparation
procedure to verify this agent's full roster, helper and source records, then
continue `/review` in this session. Refresh (run `crew install --latest --json`)
once per invocation.

Failed preparation stops required review. A `no_agents` result or another
agent's install does not prove readiness. Do not use stale or personal skills as
a fallback. See CONTRIBUTING.md#shared-local-review for setup and recovery. If
you cannot recover the preparation for any other reason, report the local review
as not run and ask the user before you open the pull request.

The shared procedure includes proposed PR title and body text from the separately
installed pull-request-description helper. This local step does not edit the PR
or change the `/review` verdict. Keep `.crewrc` and its generated lock tracked
and downloaded copies ignored. Do not commit a file under `.agents/skills/` or
`.claude/skills/`: Crew writes those copies from a private source, and this
repository is public. Do not discard a lock change yourself. The human author
commits it for review or discards it after the review. Do not refresh while
reviewers run. Do not add a script or workflow to this repository that downloads
or selects review skills. The one exception is the hosted review workflow: it sets the
`review-source` input of Pipeline's code-review action, and passes the source App
credentials that Pipeline uses to read `with-logic/skills`: the variable
`PIPELINE_SOURCE_APP_CLIENT_ID` and the secret `PIPELINE_SOURCE_APP_PRIVATE_KEY`.
