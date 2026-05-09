# ValueUp Wiki Schema

목적: 이 저장소를 Karpathy의 LLM Wiki 패턴대로 엄격하게 운영하기 위한 단일 기준 문서다.

## Non-Negotiables
- `raw/`는 불변 source layer다. ingest 후에는 수정하지 않는다.
- `wiki/`는 LLM이 유지하는 synthesis layer다. source를 복사하는 저장소가 아니다.
- 모든 durable knowledge는 `wiki/`에 남기고, 원문은 `raw/`에 둔다.
- `wiki/index.md`와 `wiki/log.md`는 항상 최신이어야 한다.
- 페이지를 만들면 반드시 index에 추가하고 log에 기록한다.

## Required Layout
- `raw/`
  - `raw/articles/`
  - `raw/papers/`
  - `raw/notes/`
  - `raw/assets/`
- `wiki/`
  - `wiki/sources/`
  - `wiki/topics/`
  - `wiki/entities/`
  - `wiki/decisions/`
  - `wiki/templates/`
  - `wiki/index.md`
  - `wiki/log.md`

## Source Rules
- Source는 원문, 변환본, 스냅샷 중 하나로 `raw/`에 보관한다.
- 파일명은 내용을 드러내는 고정형 이름으로 짓는다.
- 같은 source의 새로운 버전은 새 파일로 저장한다.
- source를 ingest하면 source page를 반드시 만든다.

## Wiki Rules
- `wiki/sources/`: 소스별 요약 페이지
- `wiki/topics/`: 여러 source를 합친 개념/주제 페이지
- `wiki/entities/`: 사람, 조직, 제품 페이지
- `wiki/decisions/`: 운영, 아키텍처, 정책 결정 기록
- 한 페이지에 사실, 해석, 미해결 질문을 뒤섞지 않는다.
- 페이지는 가능한 한 작고 조합 가능해야 한다.

## Required Page Conventions
- 모든 페이지는 짧은 목적 문장으로 시작한다.
- 기본 섹션은 다음 순서를 따른다.
  - `## Summary`
  - `## Key Points`
  - `## Details`
  - `## Sources`
  - `## Conflicts / Open Questions`
- decision page는 추가로 다음 섹션을 가진다.
  - `## Decision`
  - `## Rationale`
  - `## Risks / Mitigations`
  - `## Verification`

## Ingest Loop
새 source가 들어오면 다음 순서를 지킨다.
1. `raw/`에 source를 저장한다.
2. `wiki/sources/`에 source page를 만든다.
3. 관련 `wiki/topics/`와 `wiki/entities/`를 갱신한다.
4. 필요한 경우 `wiki/decisions/`에 결정 기록을 만든다.
5. `wiki/index.md`를 갱신한다.
6. `wiki/log.md`에 `## [YYYY-MM-DD] ingest | <title>` 형식으로 기록한다.

## Query Loop
질문에 답할 때는 다음 순서를 따른다.
1. `wiki/index.md`를 먼저 본다.
2. 관련 wiki pages를 읽는다.
3. 답변은 wiki pages와 raw source를 근거로 쓴다.
4. 답이 durable knowledge라면 wiki에 파일로 남길 수 있는지 판단한다.

## Lint Loop
정기적으로 다음을 검사한다.
- stale claims
- contradictions
- orphan pages
- missing entity pages
- missing cross-links
- `wiki/index.md` 누락 페이지
- append-only가 깨진 `wiki/log.md`

## What Not To Do
- `raw/`를 수정하지 않는다.
- source와 synthesis를 같은 페이지에 섞지 않는다.
- `index.md` 없이 새 페이지를 끝내지 않는다.
- `log.md` 없이 작업을 끝내지 않는다.
- 레포 밖의 다른 저장소 파일을 이 위키의 source로 섞지 않는다.

## Canonical Entry Points
- [AGENTS.md](./AGENTS.md)
- [wiki/index.md](./wiki/index.md)
- [wiki/log.md](./wiki/log.md)
- [wiki/templates/README.md](./wiki/templates/README.md)

