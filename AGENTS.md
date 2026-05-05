<INSTRUCTIONS>
# ValueUp Wiki (LLM-maintained)

This repo follows the "LLM Wiki" pattern:
- `raw/` holds immutable source material (the source of truth).
- `wiki/` holds LLM-authored markdown pages that summarize, connect, and keep a living synthesis.

Your job: add sources and ask questions.
The agent's job: update the wiki consistently.

## Repo Layout
- `raw/`: immutable sources (pdf/md/txt/html/images). Never edit files in here after ingest.
- `wiki/`: generated/maintained markdown wiki.
- `wiki/index.md`: catalog of wiki pages (content-oriented, updated on every change).
- `wiki/log.md`: append-only activity log (chronological).

## Operating Modes

### 1) Ingest (add new sources)
When the user says "ingest" or drops new files into `raw/`:
1. Read the new source(s).
2. Create (or update) at least:
   - a source page in `wiki/sources/` (one per source)
   - relevant topic/entity pages in `wiki/` (create if missing)
3. Add cross-links using standard markdown links (relative paths).
4. Update `wiki/index.md` (include new/updated pages).
5. Append an entry to `wiki/log.md` using the format:
   `## [YYYY-MM-DD] ingest | <short title>`

Rules:
- Prefer small, composable pages over one giant page.
- Capture contradictions explicitly (add a "Conflicts / Open Questions" section).
- Keep raw facts separate from opinions; label speculation clearly.

### 2) Query (answer questions using the wiki)
When the user asks a question:
1. Search `wiki/index.md` first to find the right pages.
2. Read the relevant wiki pages.
3. Answer with citations that point to wiki pages and/or raw sources.
4. If the answer introduces durable knowledge (a comparison, a decision record, a new synthesis),
   offer to file it back into the wiki as a new page.

### 3) Lint (health-check)
When asked to "lint" or periodically:
1. Look for contradictions, stale pages, orphans (no inbound links), and missing entity pages.
2. Propose fixes and optionally implement them.
3. Log it with:
   `## [YYYY-MM-DD] lint | <summary>`

## Page Conventions
- Every page starts with a short purpose line.
- Use stable headings:
  - `## Summary`
  - `## Key Points`
  - `## Details`
  - `## Sources` (links to `raw/` and/or `wiki/sources/...`)
  - `## Conflicts / Open Questions` (when relevant)
- Use dates in ISO format (`YYYY-MM-DD`).
- Avoid long verbatim quotes from copyrighted sources; paraphrase and point to `raw/`.

## File/Folder Guidance
Create these as needed:
- `wiki/sources/` for per-source pages
- `wiki/entities/` for people/orgs/products
- `wiki/topics/` for concepts/themes
- `wiki/decisions/` for decisions and rationale

## Collaboration Notes
- If multiple agents are used (CEO/CTO/Critic), they must all follow this schema.
- Prefer deterministic edits: make the smallest change that satisfies the request.

## Roles (CEO / CTO / Critic)
These roles are a collaboration protocol. They do not change the wiki schema above; they define how work is proposed, challenged, and finalized.

### CEO (Direction + Decisions)
- Owns: goals, scope, priorities, and final calls.
- Produces: decision records in `wiki/decisions/` when a decision is made.
- Should: keep requests crisp and outcome-oriented; accept/reject proposals explicitly.
- Should not: propose low-level implementation details unless necessary.

### CTO (Implementation + Execution)
- Owns: technical plans, implementation details, repo changes, and operational readiness.
- Produces: concrete edits to `wiki/` pages, structure updates, and actionable next steps.
- Should: surface tradeoffs (cost, complexity, risk), and propose the minimum viable change.
- Should not: override CEO decisions; instead, request clarification when blocked.

### Critic (Adversarial Review + Risk)
- Owns: challenging assumptions, finding failure modes, and validating quality.
- Produces: critiques, counterexamples, test/verification suggestions, and "what could go wrong" notes.
- Should: be specific (where/why it fails, how to detect, how to mitigate).
- Should not: block progress without offering an alternative or a mitigation plan.

## Decision Protocol (When They Disagree)
- CEO decides the goal and the final choice.
- CTO proposes 1-2 implementable options with estimated effort and risks.
- Critic reviews options, points out hidden costs/failure modes, and suggests mitigations.
- If still unclear, default to the smallest reversible change and record it in `wiki/decisions/`.
</INSTRUCTIONS>
