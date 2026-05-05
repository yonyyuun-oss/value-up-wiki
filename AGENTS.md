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

## Paperclip 이슈(티켓) 작성 포맷
Paperclip 기본 UI를 쓰더라도, 티켓 본문이 자유형이면 에이전트가 “엉뚱한 일”을 하기 쉽다.
그래서 모든 티켓에는 아래 3개 블록이 반드시 들어가야 한다.
- `TASKS`: 체크리스트(가능하면 “정확한 파일 경로” 포함)
- `DONE WHEN`: 완료 조건(acceptance criteria)
- `OUTPUT PATHS`: 결과물이 저장될 repo 경로(정확한 path)

규칙:
- 티켓에 파일 경로가 없으면 작업을 시작하지 않는다. 먼저 티켓에 `OUTPUT PATHS` 확정을 요청한다.
- 경고/징계/결정 같은 “문서” 작업은 `TYPE: decision_record`, 결과는 `wiki/decisions/`에 남긴다.
- 분석/조사/정리 같은 “지식” 작업은 `TYPE: wiki_update` 또는 `TYPE: research`, 결과는 `wiki/topics/`, `wiki/entities/`, `wiki/sources/`에 남긴다.

### (사용자용) 티켓 템플릿
아래 블록을 Paperclip 티켓 본문에 그대로 붙여넣고 채운다.

TYPE: wiki_update | decision_record | research | bugfix
OWNER: CEO | CTO | CSO
PRIORITY: P0 | P1 | P2
DUE: YYYY-MM-DD (optional)

CONTEXT:
- (왜 필요한지, 3~7줄)

INPUTS:
- (관련 이슈/문서/링크/`raw/` 경로)

TASKS:
- [ ] (할 일 1: 구체적으로)
- [ ] (할 일 2)

OUTPUT PATHS:
- (예: `wiki/decisions/2026-05-03__formal-warning-alex-kim-pue-493.md`)
- (예: `wiki/index.md`, `wiki/log.md`)

DONE WHEN:
- (예: 위 파일이 “정확한 경로”에 존재)
- (예: 문서에 `Verification` 섹션 포함)
- (예: `wiki/index.md`에 링크 추가)
- (예: `wiki/log.md`에 새 엔트리 추가)

NOTES:
- (제약, 문체/톤, 금지사항 등)

### (에이전트용) 티켓 생성 템플릿
에이전트가 스스로 “해야 할 일”을 발견해서 티켓을 등록할 때는 아래를 지킨다.
- `TASKS`는 1~5개로 작게 쪼갠다(큰 작업이면 티켓을 여러 개로 쪼갠다).
- `OUTPUT PATHS`는 반드시 “파일 경로”로 고정한다(“문서 업데이트” 같은 표현 금지).
- `DONE WHEN`은 검증 가능해야 한다(눈으로 확인 가능한 조건).
- 결정이 필요한 사안이면 `OWNER: CEO`로 올리고, 에이전트는 임의로 최종 결정을 확정하지 않는다.

에이전트용 예시:

TYPE: decision_record
OWNER: CEO
PRIORITY: P1

CONTEXT:
- refresh_token 공유로 인해 만료 시점에 3개 컨테이너 중 일부가 token rotation으로 영구 실패할 수 있음.

INPUTS:
- 관련 논의: (티켓/링크)

TASKS:
- [ ] `wiki/decisions/2026-05-05__oauth-refresh-single-writer.md` 작성(템플릿 준수)
- [ ] `wiki/index.md`에 decision record 링크 추가
- [ ] `wiki/log.md`에 `## [2026-05-05] decision | OAuth refresh single-writer` 기록 추가

OUTPUT PATHS:
- `wiki/decisions/2026-05-05__oauth-refresh-single-writer.md`
- `wiki/index.md`
- `wiki/log.md`

DONE WHEN:
- decision record에 `Risks / Mitigations`, `Verification` 섹션이 존재
- `wiki/index.md`에서 decision record로 이동 가능한 링크 존재
- `wiki/log.md`에 새 엔트리가 추가됨
</INSTRUCTIONS>
