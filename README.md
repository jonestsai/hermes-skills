# hermes-skills

Personal [Hermes Agent](https://hermes-agent.nousresearch.com/) skills, installable on any machine via the skills tap system.

## Skills

| Skill | Category | What it does |
|---|---|---|
| [poteto-mode](software-development/poteto-mode/) | software-development | Rigorous engineering workflows (playbooks, principles, adversarial review) — a Hermes port of poteto's pstack. Explicit trigger only: say "poteto-mode: <task>". |

## Install (on any machine with Hermes)

```bash
# register this repo as a skill source (once per machine)
hermes skills tap add jonestsai/hermes-skills

# install a skill
hermes skills install jonestsai/hermes-skills/software-development/poteto-mode
```

Layout mirrors Hermes's local skill structure (`<category>/<skill-name>/SKILL.md`), so installed copies land exactly where local ones would.

## Updating

```bash
hermes skills check    # see what's outdated
hermes skills update   # pull latest versions
```

## License & attribution

- **poteto-mode** is a port of [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan (poteto), MIT licensed. Its `deslop` reference comes from [cursor-team-kit](https://github.com/cursor/plugins/tree/main/cursor-team-kit), also MIT. All credit for the underlying methodology to poteto and the Cursor team; this repo only adapts the invocation surface to Hermes Agent.
- Repo itself: MIT.
