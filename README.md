# crew-skills

The default skills collection shipped with [`crew`](https://github.com/with-logic/crew) — the package manager for [Agent Skills](https://agentskills.io).

When you install `crew`, this repo is wired up as your `core` tap. `crew search` and `crew install <name>` pull from here unless you add more taps.

## Install a skill

```sh
crew install <skill-name>
```

`crew` figures out where each skill belongs for every agent coder on your machine (Claude Code, Codex, Cursor, Gemini CLI, Goose, GitHub Copilot, and others) and installs it in one command.

## Browse what's here

Each immediate subdirectory of this repo is one skill. Drop into any of them to read its `SKILL.md`.

You can also search from the command line:

```sh
crew search <query>
```

## Contribute a skill

1. Fork the repo.
2. Add a new directory at the root named after your skill (lowercase, hyphens, e.g. `python-testing`).
3. Inside it, write a `SKILL.md` that follows the [Agent Skills specification](https://agentskills.io/specification). At minimum:
   ```yaml
   ---
   name: python-testing
   description: Run and write Python tests the way this team does.
   ---
   ```
4. Open a pull request.

Each skill should be self-contained (a single directory with `SKILL.md` plus any supporting files it references). See the spec for supporting-file conventions, templates, and frontmatter options.

## License

MIT — see [LICENSE](LICENSE).
