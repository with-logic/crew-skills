---
name: tangleguard
description: >-
  Inspect a codebase's architecture — layers, packages, the package dependency
  graph, rule violations, and circular dependencies — and run preflight checks
  before changing it: whether a dependency is allowed, whether it would create a
  cycle, and where a piece of code belongs. Query the TangleGuard CLI instead of
  reading files or guessing. Use BEFORE adding a dependency between modules,
  moving code between layers, or any structural / architectural decision, and
  whenever you need to understand how an unfamiliar codebase is organized.
---

# TangleGuard: Query the Architecture, Don't Guess It

TangleGuard is a static analysis tool (native Rust, extremely fast) that
extracts structural artifacts from source code. Use its CLI to get
deterministic answers about how the code is organized — and about whether a
change you are about to make is allowed — instead of grepping across files and
inferring the structure yourself. This keeps token usage low and your reasoning
grounded in facts rather than guesses.

## When to Use This Skill

Reach for `tangleguard-cli` whenever a task depends on the _structure_ of the
codebase, in particular:

- **Before introducing a new dependency** between modules or packages — check
  whether it is allowed (`check-dependency`) and whether it would create a cycle
  (`would-create-cycle`), _before_ writing the import.
- **When deciding where new code belongs** — ask `placement` for a node's layer
  and the full set of things it may depend on (and that may depend on it).
- **Before a structural change or refactor** — moving code between layers,
  splitting or merging packages, renaming modules.
- **When making an architectural or design decision** — consult the real
  layers, the allowed dependency rules, and the package graph first.
- **When orienting in an unfamiliar codebase** — get the big picture in a
  single call instead of reading dozens of files.
- **After a change, to confirm you did not break the architecture** — run the
  validation commands.

If you only need the structure, prefer asking TangleGuard over reading source
files: it is faster, deterministic, and far cheaper in tokens.

## How to Use It

The binary is `tangleguard-cli`. Most commands need the language (`-l`) and
optionally a path (`-p`, defaults to the current directory). The _config-only_
preflight checks below (`placement`, `check-dependency`) read `tangleguard.json`
and do **not** scan, so they need no `-l`. Output is markdown by default, ready
to read or drop straight into your reasoning. Add `--format json` when you need
to parse it programmatically.

### Inspect the architecture

```bash
tangleguard-cli -l <language> [-p <path>] architecture
```

Prints a compact summary: the layers, the allowed dependency rules, the
packages (with their resolved layer and lines of code), and the package-level
dependency graph. Run this first to understand the system.

### Check Before You Change It (Preflight)

These answer "may I?" _before_ you write the code, so you never introduce a
violation or a cycle in the first place. A `<node>` is a workspace path like
`apps::web` or a layer name like `Apps`.

```bash
# Where does a node belong, and what may it wire to? (config-only, no -l)
tangleguard-cli [-p <path>] placement --node <node>

# Is a dependency edge allowed by the rules? (config-only, no -l)
tangleguard-cli [-p <path>] check-dependency --from <node> --to <node>

# Would adding a dependency create a circular dependency? (scans, needs -l)
tangleguard-cli -l <language> [-p <path>] would-create-cycle --from <node> --to <node>
```

- **`placement`** lists the node's layer plus every layer/path it may depend on,
  and every one that may depend on it — each annotated with the rule that
  permits it. Ask this when orienting ("where does this belong and what can it
  use?") instead of probing edges one at a time.
- **`check-dependency`** reports allowed or denied for a single edge; a denial
  also lists the allowed alternatives from the source.
- **`would-create-cycle`** reports whether the edge closes a loop and prints the
  existing path it would close.

Add `--ci` to `check-dependency` / `would-create-cycle` to exit non-zero on a
denied edge or a would-be cycle (useful as a commit/CI guardrail).

### Validate the Architecture

```bash
tangleguard-cli -l <language> [-p <path>] validate         # rule violations + cycles
tangleguard-cli -l <language> [-p <path>] validate-rules   # rule violations only
tangleguard-cli -l <language> [-p <path>] check-circles    # circular dependencies only
```

Violations come back with `file:line` evidence, so you can jump straight to the
offending code.

Supported languages include `rust`, `go`, `typescript` / `javascript`,
`python`, `kotlin`, and `php`.

## A Typical Flow

1. The task touches the architecture → run `architecture` to learn the layers,
   the rules, and the package graph.
2. Before adding a dependency, ask the preflight checks: `placement` to see
   where code belongs and what it may use, `check-dependency` to confirm a
   specific edge is allowed, and `would-create-cycle` to be sure it won't close
   a loop. Decide against the real structure, not a guess.
3. Make the change.
4. Run `validate` to confirm you introduced no rule violations or cycles, and
   fix anything it reports at the given `file:line`.
