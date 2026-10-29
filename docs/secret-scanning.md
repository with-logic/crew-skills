# Secret scanning in crew-skills

Secret-scan results are advisory for merging into `main`; scans and #security
alerts continue.

The **Secret scan** check runs native, MIT-licensed Gitleaks 8.30.1 on PRs,
branch/tag pushes, merge queues, and manual runs. PR, merge-queue, and pushed-range
findings fail the scan job. Findings from full-history scans warn during historical
triage. Scanner, Git, and configuration errors still fail. Gitleaks recognizes credential
patterns; it does not verify whether a match is a live, private secret.

PRs scan base-to-head history, so adding a credential and deleting it in a later
commit still fails. Pushes scan the pushed range; first pushes to non-default
branches scan from the merge base with the fetched default branch, excluding
inherited history. Missing required bases fail. New tag pushes, first
default-branch pushes, and manual runs scan history reachable from the selected
head. Tag updates scan only the pushed range, where findings fail the scan job.
Ignored and untracked local files such as `.env` are outside the scan. A
force-added or previously tracked `.env` is Git content and is checked.

Image preparation and checkout happen before scanning. The scan runs with
networking disabled, a read-only source mount, and no service credentials. No
Gitleaks account or license signup is needed. A separate `pull_request_target`
run uses trusted workflow code to scan PR data and comment on findings/errors,
including fork PRs. It never runs project scripts, dependencies, or local actions.
These comments start after the workflow reaches the default branch. They link to
rule, introducing commit, and line metadata in the run log. File paths and
fingerprints are omitted from this public log because native `--redact=100`
does not redact sensitive text in those fields.

Each PR, push, merge-queue event, and manual dispatch starts an independent scan.
Guidance is best effort: the head is checked immediately before posting. A push
can still race GitHub's separate head-check and comment requests; each comment
identifies its scanned commit.

## Investigate a finding

The public log shows the rule, introducing commit, and line. Use the
[guarded local report command](#local-scans) to obtain file paths and native
`Fingerprint` values (the **Ignore IDs** used by `.gitleaksignore`). Keep that
report private: these fields are not redacted.
Inspect the reported location privately; do not paste possible credentials into
PRs, Slack, or issues.

- **Real credential:** revoke or rotate it if exposed, replace it with an
  environment-variable or secret-store reference, and remove it from every
  introducing PR commit. Coordinate rewriting shared history with the owner;
  deletion or a history rewrite does not replace rotation.
- **Confirmed public, placeholder, or test value:** document why it is safe and
  obtain review for a narrow native exception below. Do not baseline untriaged
  historical matches to make CI green.
- **Scanner error:** repair the checkout/history, image/runtime, or configuration
  problem and rerun. No clean result was obtained; an exception is not a repair.

## Per-repository exceptions

Each repo owns its root `.gitleaks.toml` and `.gitleaksignore`. Native Gitleaks
reads policy from the scanned checkout, including inline `gitleaks:allow`
comments. A proposed exception can therefore affect its own PR scan. These
changes need trusted human review, just like workflow changes; CI does not
technically enforce exception approval. Scan results remain advisory for merging.

For one historical finding, copy its complete `Fingerprint` from the private JSON
report into `.gitleaksignore`, one per line, with the reason and review reference
on a separate comment line:

```text
# Generated frontend demo value; reviewed in <review link>.
<commit>:<path>:<rule>:<line>
```

This finding fingerprint is not a file hash or whole-file exclusion.
Rebase/history changes or a new introduction can produce a different fingerprint.

For recurring verified public/test data, preserve default rules and allow only
the complete approved value for each matching rule:

```toml
[extend]
useDefault = true

[[allowlists]]
description = "Public fixture; reviewed by <reviewer> in <review link>, <date>"
targetRules = ["generic-api-key"]
regexTarget = "secret"
regexes = ['^public-example-value$']
```

Escape regex metacharacters in the approved value and avoid wildcards or broad
file exclusions. A replacement value remains checked. Preserve existing config
when adding an exception; exceptions apply only to this repository.

## Local scans

Install the native CLI once with `brew install gitleaks`, which installs the
current Homebrew formula version. For exact CI parity, install the
official [Gitleaks 8.30.1 release](https://github.com/gitleaks/gitleaks/releases/tag/v8.30.1).

From the repository root, scan committed history and write a redacted diagnostic
report outside the checkout. The native scan runs offline, forces Git text output,
and disables textconv and external diff drivers:

```sh
(
  set -e
  umask 077
  report_dir=$(mktemp -d /tmp/crew-skills-gitleaks.XXXXXX)
  printf 'Private report directory: %s\n' "$report_dir"
  scan_stderr="$report_dir/errors"
  scan_status=0
  GIT_NO_LAZY_FETCH=1 \
  GIT_CONFIG_PARAMETERS="${GIT_CONFIG_PARAMETERS:+$GIT_CONFIG_PARAMETERS }'color.ui=false' 'color.diff=false'" \
  gitleaks git --redact=100 --no-banner --log-level error \
    --log-opts="HEAD --full-history --diff-merges=remerge --text --no-ext-diff --no-textconv" \
    --report-format json --report-path "$report_dir/findings.json" \
    2>"$scan_stderr" || scan_status=$?
  # Gitleaks can log Git-source errors on stderr and still exit zero.
  if [ -s "$scan_stderr" ]; then
    cat "$scan_stderr" >&2
    if [ "$scan_status" -eq 0 ]; then scan_status=1; fi
  fi
  exit "$scan_status"
)
```

The directory is mode `700`; its report and diagnostic files are mode `600`.
Findings and scanner errors return a nonzero status. The report and diagnostics
remain available for inspection; remove the private directory when finished.

This command does not install hooks. See the [native Gitleaks guide](https://github.com/gitleaks/gitleaks/tree/v8.30.1#readme)
for CLI details. The organization-wide twelve-hour history sweep reports findings
to #security.
