# Value-Up Hermes Hindsight Architecture

Purpose: detailed record of the current Hermes / Hindsight / NATS architecture, the cost constraints that shaped it, and the verification results that made it safe to keep building on.

## Why this changed
- The operator is already paying for both Claude and Codex subscriptions and wants to avoid a third API bill if possible.
- The desired split is not “one provider for everything”.
- The target operating pattern is:
  - `CEO` and `Hindsight` should prefer Claude
  - `CTO` and `Critic` should prefer Codex
- The architecture therefore needed to reuse subscription auth directly rather than introduce a new paid API path.

## What the system is now doing
- `Hermes CEO` runs with `provider=anthropic` and `model=claude-sonnet-4-6`.
- `Hermes CTO` runs with `provider=openai-codex` and `model=gpt-5.3-codex`.
- `Hermes Critic` runs with `provider=openai-codex` and `model=gpt-5.4-mini`.
- `Hermes` runtime memory is enabled via `memory.provider = hindsight`.
- Hindsight is configured as a local external daemon per role.
- Hindsight uses Claude Code auth directly with `claude-code` and `claude-sonnet-4-6`.
- The NATS bridge only invokes the external Hermes wrappers; it does not synthesize Hermes output anymore.

## What we learned about provider support
- Hermes itself already supports Anthropic directly, so no proxy was needed for the CEO role.
- Hindsight local embedded mode also supports Anthropic and Claude Code provider paths.
- The shortest no-extra-API route was not an OpenAI-compatible proxy.
- The direct Claude Code route was better:
  - no extra service to maintain
  - no extra API charge
  - no extra translation layer

## Why we did not keep embedded local mode
- Hindsight embedded mode could initialize the Claude Code provider, but it failed in this environment because it tried to start embedded PostgreSQL as root.
- The failure mode was:
  - `initdb: error: cannot be run as root`
- That made embedded mode too fragile for the current host/container setup.
- The practical fix was to run Hindsight as a local external daemon instead.

## Final Hindsight shape
- Each Hermes role container now has:
  - `memory.provider = hindsight`
  - a local external Hindsight daemon on `127.0.0.1:8899`
  - Hindsight package dependencies installed inside the container
- Each daemon is started as the non-root `hermes` user.
- Each daemon uses:
  - `HINDSIGHT_API_LLM_PROVIDER=claude-code`
  - `HINDSIGHT_API_LLM_MODEL=claude-sonnet-4-6`
- This keeps Hindsight on the same Claude subscription path the operator already pays for.

## Role routing
- `CEO`:
  - main model: `claude-sonnet-4-6`
  - auxiliary tasks pinned to Anthropic
- `CTO`:
  - main model: `gpt-5.3-codex`
- `Critic`:
  - main model: `gpt-5.4-mini`

## Verification
- `hermes memory status` shows `hindsight` active in each role container.
- `role_smoke.sh` passes with:
  - `CEO` on `anthropic / claude-sonnet-4-6`
  - `CTO` on `openai-codex / gpt-5.3-codex`
  - `Critic` on `openai-codex / gpt-5.4-mini`
- `flow_value_up_smoke.sh` passes and emits:
  - `company.value-up.paperclip.issue.created`
  - `company.value-up.hermes.agent.run.started`
  - `company.value-up.hermes.agent.run.completed`
  - `company.value-up.audit.event.recorded`
- The worker also skips duplicate issue events via the bridge ledger.

## Practical value
- The current setup reuses existing paid subscriptions rather than adding another API meter.
- The routing is simple enough to keep reasoned about:
  - Claude for CEO and Hindsight
  - Codex for CTO and Critic
- The system is stable enough to keep building Hermes-specific workflows on top of it.

## Remaining risks
- The Hindsight daemons are still a runtime dependency per role and should be watched for startup drift.
- Role-specific prompt and workflow tuning is still not finished.
- Broader Hermes surface integration is still ahead.

## Files changed in the main repo
- `~/value-up/hermes/bootstrap_hindsight.sh`
- `~/value-up/hermes/STATUS.md`
- `~/value-up/hermes/README.md`
- `~/value-up/infra/nats/SESSION_HANDOFF.md`

## Related wiki pages
- [Hermes role routing and progress snapshot](./2026-05-05__value-up-hermes-role-routing-and-progress-snapshot.md)
- [Activity log](../log.md)
