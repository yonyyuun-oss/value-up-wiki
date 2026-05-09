# Karpathy LLM Wiki Gist Snapshot

목적: 이 저장소가 따르는 LLM Wiki 패턴의 기준 source를 raw 레이어에 보존한다.

## Snapshot
- 이 문서는 Karpathy의 LLM Wiki gist를 기반으로 한 요약 스냅샷이다.
- 핵심 구조는 `raw/`-`wiki/`-`schema`의 3층 구성이다.
- `raw/`에는 불변 source를 두고, `wiki/`에는 LLM이 유지하는 합성 결과를 둔다.

## Core Ideas
- source를 매번 다시 찾지 말고, 누적된 wiki를 유지한다.
- `index.md`는 content catalog다.
- `log.md`는 append-only timeline이다.
- ingest, query, lint를 분리하면 운영이 안정된다.

## Operational Notes
- ingest는 source page, topic/entity page, index, log를 함께 갱신한다.
- query는 index부터 읽고, 관련 wiki pages를 따라가며 답한다.
- lint는 stale claims, orphans, missing links, missing pages를 점검한다.

## Sources
- external: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

