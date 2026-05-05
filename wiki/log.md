Purpose: append-only chronological log of wiki operations (ingests, queries, lint passes).

## [2026-05-05] init | Repo scaffold
- Added initial `AGENTS.md` schema and base wiki structure.

## [2026-05-05] decision | Hermes role routing snapshot
- Recorded the current Hermes model split, the NATS worker provider override fix, and the open Hindsight routing question.

## [2026-05-05] verify | CEO auxiliary pinning
- Pinned CEO auxiliary tasks to Anthropic and re-ran the Paperclip -> Hermes -> Audit smoke successfully.

## [2026-05-05] verify | Hindsight Claude Code path
- Installed the Hindsight local external runtime, activated the provider, and verified `claude-code / claude-sonnet-4-6` works directly with Claude Code auth.

## [2026-05-05] verify | Hindsight role daemons
- Bootstrapped per-role local external Hindsight daemons, installed `hindsight-all` into each Hermes container, and enabled `memory.provider = hindsight` for CEO, CTO, and Critic.

## [2026-05-05] decision | Hermes Hindsight architecture detail
- Added a detailed architecture note covering the Claude subscription reuse path, the embedded-mode PostgreSQL failure, the local external daemon fallback, and the final role-by-role verification state.
