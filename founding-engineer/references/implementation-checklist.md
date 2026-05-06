# Implementation Checklist

Use this before finalizing any non-trivial change.

## 1. Product contract
- What behavior changed?
- For whom?
- What must remain stable?
- Is there a simpler interpretation of the ask that solves the real problem better?

## 2. Correctness
- Does the change handle empty, invalid, duplicate, and partial input?
- Are defaults correct?
- Are error paths explicit?
- Are retries safe and idempotent where needed?
- Could concurrency, stale state, or ordering break this?

## 3. Compatibility
- Is this path already in production?
- Did any request/response/event/schema/default/nullability contract change?
- Are older callers or stored records still valid?
- Is a compatibility shim or migration needed?

## 4. Data and migrations
- Was schema change additive where possible?
- Is backfill required?
- Is rollback possible?
- Did cache keys, indexes, derived data, or read models change?

## 5. Security and privacy
- Is every external input validated?
- Are authn/authz expectations preserved?
- Could this leak secrets or PII?
- Are logs and analytics safe to emit?

## 6. Observability
- Can production tell success from failure?
- Are the most important failures diagnosable?
- Is a dashboard, alert, or metric implied by this change?
- Is there enough context in logs without spamming?

## 7. Performance
- Is this a hot path or large batch path?
- Did query count, payload size, or render churn worsen?
- Are there obvious N+1 or repeated-work risks?

## 8. Tests
- Is there coverage for the happy path?
- Edge cases?
- Error paths?
- Regression case for the bug or spec gap?
- Compatibility coverage where needed?
- Do the tests prove behavior rather than internals?

## 9. Rollout
- Should this be behind a feature flag?
- Is there a kill switch or easy rollback?
- Is staged rollout needed?
- What should be watched after deploy?

## 10. Repo fit
- Does this follow existing patterns unless there is a strong reason not to?
- Did you avoid speculative abstractions?
- Is the diff coherent and surgically scoped?
- Did you notice an adjacent lake worth boiling?
