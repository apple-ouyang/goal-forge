# goal-forge

Codex skill that turns a rough coding idea into a Codex `/goal`-ready contract.

Pipeline: **rough idea → interviewed `SPEC.md` → tightened `SPEC.md` → `GOAL.md` → config readiness check**.

## Modes

- **Interview** — open-ended interview that forces decisions on scope, architecture, edge cases, verification. Hard gate: spec is not "done" until `done_when` has user-approved measurable criteria.
- **Tighten** — read `SPEC.md` skeptically; surface ambiguities with two interpretations + a recommendation.
- **Compile** — emit `GOAL.md` using the XML block structure in `references/goal_prompt_blocks.md`. Weak specs route back to Interview/Tighten.
- **Check config** — run `scripts/inspect_codex_config.py` for a read-only report on Codex version, project trust, and long-running `/goal` settings.

## Install

Drop the folder into your Codex skills directory:

```bash
git clone https://github.com/michaelpersonal/goal-forge.git ~/.codex/skills/goal-forge
```

Then invoke from Codex with `$goal-forge` or any of the natural-language triggers in `SKILL.md`'s frontmatter.

## Layout

```
goal-forge/
├── SKILL.md
├── agents/openai.yaml                       UI metadata + implicit invocation
├── references/
│   ├── goal_prompt_blocks.md                GOAL.md XML structure
│   ├── config_checklist.md                  Long-running /goal config notes
│   └── standard_execution_rules.md          Compile-time execution rules
└── scripts/
    └── inspect_codex_config.py              Read-only config readiness report
```

## License

MIT. See `LICENSE`.
