목적: Karpathy의 LLM Wiki gist를 이 저장소의 기준 source로 요약한다.

# Karpathy LLM Wiki Gist

## Summary
- 이 gist는 `raw/`-`wiki/`-`schema`의 3층 구조를 제안한다.
- 핵심은 원문을 불변 소스로 두고, LLM이 위키를 누적 편집하는 것이다.
- `index.md`와 `log.md`가 위키의 탐색성과 회복력을 담당한다.

## Key Points
- `raw/`는 source of truth다.
- `wiki/`는 요약, 연결, 합성을 담당한다.
- schema 문서가 LLM의 작업 규칙을 강제한다.
- ingest, query, lint를 분리하면 운영 루프가 단순해진다.

## Details
- ingest: 새 source를 읽고 source page, topic/entity page, index, log를 함께 갱신한다.
- query: 먼저 index를 보고, 필요한 wiki pages를 읽고, 근거를 붙여 답한다.
- lint: orphans, stale claims, missing links, missing pages를 점검한다.
- 필요하면 wiki 바깥의 CLI 도구를 붙여 검색이나 검증을 보강할 수 있다.

## Sources
- [Karpathy gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [raw snapshot](../../raw/notes/2026-05-09__karpathy-llm-wiki-gist.md)
- [AGENTS.md](../../AGENTS.md)

## Conflicts / Open Questions
- `raw/`에는 gist의 요약 스냅샷을 넣었지만, 원문 전체를 그대로 저장할지 여부는 아직 정하지 않았다.
- source capture를 원문 보존과 요약 스냅샷 중 어떤 기준으로 표준화할지 다음 ingest에서 더 좁혀야 한다.
