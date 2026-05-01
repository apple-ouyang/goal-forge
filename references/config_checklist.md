# Codex Goal Config Checklist

Use this checklist when preparing long-running Codex `/goal` work.

## Required Checks

- Codex CLI version supports `/goal`.
- The project path is trusted.
- Git status is understood before the run starts.
- The working tree does not contain unrelated risky changes.
- The spec has user-approved measurable `done_when` criteria.
- Verification commands are known and can run without interactive prompts.

## Long-Running Run Settings

Useful settings to inspect in `~/.codex/config.toml`:

```toml
model = "gpt-5.5"
model_reasoning_effort = "high"
plan_mode_reasoning_effort = "xhigh"
model_auto_compact_token_limit = 997500

[features]
goals = true
```

Autonomous runs may also use:

```toml
approval_policy = "never"
sandbox_mode = "danger-full-access"
```

Only recommend those settings for trusted project directories. They allow hands-off execution and broad filesystem access, so they are inappropriate for untrusted code, sensitive credentials, payment/auth paths, or tasks that should pause for approval.

## Safer Middle Ground

For goals that need repo file writes but should not have broad system access, recommend a narrower profile:

```toml
approval_policy = "on-failure"
sandbox_mode = "workspace-write"
```

Use the most permissive settings only when the goal truly needs unattended execution and the project directory is trusted.

## Recommendation Style

Report gaps and risks. Do not silently change config.

If `/goal` is unavailable, still generate `GOAL.md`; tell the user to update Codex before running it as a persisted goal.
