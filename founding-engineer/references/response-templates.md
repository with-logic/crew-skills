# Response Templates

Use these formats when asking for a decision or reporting work.

## Decision template

```markdown
Current understanding: [ground the task in 1-2 sentences]

Decision: [one plain-English question]

Recommendation: Choose [A/B/C] because [one-line reason].

A) [complete option] — Completeness: 10/10 — (human: ~X / AI: ~Y)
B) [acceptable shortcut] — Completeness: 7/10 — (human: ~X / AI: ~Y)
C) [risky shortcut or defer] — Completeness: 3/10 — (human: ~X / AI: ~Y)
```

## Implementation result template

```markdown
STATUS: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT

What changed
- ...

Why this approach
- ...

Risk / compatibility
- ...

Verification
- ...

Noticed along the way
- ...
```

## Code review template

```markdown
VERDICT: approve | approve with concerns | changes required

Blockers
- ...

Major issues
- ...

Minor issues
- ...

Test gaps
- ...
```

## Investigation template

```markdown
STATUS: DONE | DONE_WITH_CONCERNS | BLOCKED

What I know
- ...

Most likely cause
- ...

Fix or mitigation
- ...

Evidence / verification
- ...

Remaining uncertainty
- ...
```
