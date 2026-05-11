---
name: goal-forge
description: Turn a rough coding or repo idea into a spec, executable plan, and long-running Codex /goal contract. Use when the user wants to create a coding spec, spec out a repo feature, tighten a coding spec, turn an implementation idea into a /goal-ready plan, prepare for autonomous coding, forge a goal, start a /goal, generate GOAL.md, prepare measurable done_when criteria, or check Codex config readiness.
---

# Goal Forge

Goal Forge converts a rough coding idea into an executable Codex `/goal` contract.

This fork stays intentionally close to `michaelpersonal/goal-forge`; keep local changes minimal and review upstream before updating.

Default pipeline:

1. Run `docs-list <repo-root>` and choose or create the active plan document under `docs/plans/`.
2. Interview rough intent into that plan document.
3. Tighten the plan until scope and verification are concrete.
4. Compile the approved plan into the same document or a sibling goal document under `docs/plans/`.
5. Check Codex config readiness before running `/goal`.

Use this skill for coding and repo work first. If the task is mostly exploratory, research-only, security-critical, or lacks verifiable completion criteria, keep the user in the loop instead of compiling a `/goal`.

## Modes

Choose the smallest mode that satisfies the request.

- **Interview**: User gives a rough idea, asks to create a spec, or the plan lacks user-approved success criteria.
- **Tighten**: User has a plan document and wants it challenged, clarified, or made executable.
- **Compile**: User has a good enough plan document and wants a `/goal` prompt or goal document.
- **Check config**: User asks whether their Codex setup can run long goals.

## Document System

Always use the repository docs system for durable Goal Forge artifacts.

1. Run `docs-list <repo-root>` before reading, creating, or updating plan content.
2. Reuse an existing active plan when it fits the task. If no active plan fits, create a new file under `docs/plans/`.
3. Do not create root-level `SPEC.md` or `GOAL.md`.
4. Use `doc_kind: plan` and `status: active` in front matter.

Plan documents must include at least this front matter:

```yaml
---
summary: <one sentence>
doc_kind: plan
status: active
read_when:
  - <when to read this plan>
---
```

## Interview Mode

When the user gives a rough idea or asks to create a spec:

1. Run `docs-list <repo-root>`, then read the chosen active plan document if present. Otherwise treat the rough idea as the interview seed; do not draft the plan until step 6.
2. Interview the user in detail before finalizing the spec.
3. Ask about anything relevant: technical implementation, UI/UX, architecture, data model, edge cases, tradeoffs, rollout, verification, risks, and non-goals.
4. Do not ask obvious checklist questions. Ask questions that force decisions or expose hidden ambiguity.
5. Continue interviewing until the spec is complete enough to compile into an executable `/goal`.
6. Then write or update the selected `docs/plans/` plan document.

Use structured user-question tooling when available. If it is not available, ask concise batches of questions in chat. Keep each batch focused on unresolved blockers.

Hard gate: do not compile a `/goal` prompt until `done_when` contains concrete, user-approved success criteria. After the interview has covered scope, architecture, and verification, Codex may propose candidate `done_when` criteria as a starting point. The user must confirm or edit product and acceptance criteria before the spec is considered complete.

## Tighten Mode

Read the selected `docs/plans/` plan document skeptically before any execution prompt is created.

For each ambiguity:

- explain why it matters
- give two distinct interpretations
- recommend the interpretation that produces the more maintainable result
- ask the user when the choice defines product intent
- update the spec only after the choice is clear

Do not add scope. Remove or mark anything that cannot be verified. Preserve clear existing decisions.

The tightened spec should include:

- goal and non-goals
- user-visible behavior
- architecture and implementation constraints
- files, modules, or systems likely involved
- explicit edge cases
- risk boundaries
- verification commands or manual checks
- user-approved `done_when` criteria

## Compile Mode

Compile the selected `docs/plans/` plan document into a `/goal` prompt or a sibling goal document under `docs/plans/` using the block structure in `references/goal_prompt_blocks.md`. Load `references/standard_execution_rules.md` for default `<execution_rules>` content.

Before writing a goal document, reject weak plans that lack:

- measurable `done_when`
- scope boundaries or non-goals
- concrete verification commands or checks
- enough context for an agent to start reading the repo

If the plan is weak, route the user back to Interview or Tighten mode with a specific list of missing decisions. Do not write the goal document until the gaps are resolved.

Concrete gate: each `done_when` item must name a command, file artifact, or user-observable behavior. Otherwise mark it as weak and ask for clarification.

Map the spec into the goal blocks:

- spec summary -> `<goal>`
- relevant files/modules/discovery commands -> `<context>`
- architecture rules, non-goals, anti-patterns -> `<constraints>`
- acceptance criteria -> `<done_when>`
- implementation phases -> `<workflow>`
- test/build/manual checks -> `<verification_loop>`
- `references/standard_execution_rules.md` plus repo-specific rules -> `<execution_rules>`
- final artifacts and completion signal -> `<output_contract>`

After writing the goal document, self-check it:

- `done_when` is measurable and user-approved
- context names files or discovery commands
- constraints prevent obvious shortcuts
- verification is executable or explicitly manual
- the task is execution-oriented enough for `/goal`

## Config Check

Use `scripts/inspect_codex_config.py` for a read-only report:

```bash
python3 ~/.codex/skills/goal-forge/scripts/inspect_codex_config.py --project-path /path/to/project
```

Run it from the target project root, or pass `--project-path`. The script reports the installed Codex version, selected config keys, trusted-project status, and gaps against the autonomous `/goal` configuration.

Before telling the user a long-running `/goal` is ready, verify the config against `references/config_checklist.md`. The important autonomous settings are:

- `model = "gpt-5.5"`
- `model_context_window = 1050000`
- `model_auto_compact_token_limit = 997500`
- `model_reasoning_effort = "high"`
- `plan_mode_reasoning_effort = "xhigh"`
- `approval_policy = "never"`
- `sandbox_mode = "danger-full-access"`
- `[features] goals = true`

Do not edit `~/.codex/config.toml` unless the user explicitly asks for config changes. If asked to change config, prefer applying the dangerous `approval_policy = "never"` and `sandbox_mode = "danger-full-access"` settings only for explicitly trusted project paths rather than globally.

Read `references/config_checklist.md` when explaining config tradeoffs.

## Output

Depending on the request, produce one or more of:

- updated `docs/plans/` plan document
- generated goal document under `docs/plans/`
- config readiness report
- the exact `/goal` prompt body to paste into Codex

When the user asks to run the goal, verify that their Codex version supports `/goal` first.
