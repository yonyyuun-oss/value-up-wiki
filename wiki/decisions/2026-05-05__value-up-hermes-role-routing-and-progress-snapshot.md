Purpose: record the current Hermes role routing, the NATS worker provider fix, and the Hindsight Claude Code memory daemon path.

## Summary
- `CEO` is now configured as `anthropic / claude-sonnet-4-6`.
- `CTO` remains `openai-codex / gpt-5.3-codex`.
- `Critic` remains `openai-codex / gpt-5.4-mini`.
- The NATS Hermes worker had been defaulting to `openai-codex`, which overrode the CEO container config and caused a `claude-sonnet-4-6` 400 error.
- CEO auxiliary tasks are now pinned to Anthropic too, which removed the Codex fallback from the smoke path.

## Key Points
- The simplest stable behavior is to let each Hermes container own its own `model.provider` and `model.model`.
- The bridge should only pass `--provider` / `--model` when an explicit override is required.
- Hindsight now runs as a local external daemon per role, using `claude-code / claude-sonnet-4-6` directly so it can reuse Claude subscription auth without a separate proxy.
- The current stopgap is to keep Hermes routing container-local and avoid a worker-side default provider.

## Details
- The worker default provider was removed so `hermes_ceo` now uses the container's configured provider/model directly.
- This keeps the NATS bridge generic and avoids accidentally forcing every role through the same provider.
- If we later add a separate Hindsight proxy layer, that will only be for a non-Claude provider path.
- The CEO container now pins auxiliary tasks like compression and session search to Anthropic so they do not auto-fallback to Codex.

## Sources
- [Hermes worker](../../../value-up/infra/nats/hermes_worker_value_up.sh)
- [Hermes bootstrap](../../../value-up/hermes/bootstrap_hindsight.sh)
- [Hermes roles](../../../value-up/hermes/ROLES.md)
- [Hermes status](../../../value-up/hermes/STATUS.md)
- [Session handoff](../../../value-up/infra/nats/SESSION_HANDOFF.md)

## Conflicts / Open Questions
- Whether Hindsight should also support a Codex-backed or OpenAI-compatible fallback remains open.
- The CEO Anthropic route and the NATS smoke are now verified after the auxiliary pinning change.

## Related Detail
- [Value-Up Hermes Hindsight Architecture](./2026-05-05__value-up-hermes-hindsight-architecture.md)
