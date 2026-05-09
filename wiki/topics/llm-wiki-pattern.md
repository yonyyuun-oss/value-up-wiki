목적: 이 저장소가 따르는 LLM Wiki 운영 패턴을 정리한다.

# LLM Wiki Pattern

## Summary
- 이 저장소는 `raw/`에 불변 소스를 두고, `wiki/`에서 누적 합성을 유지하는 패턴을 따른다.
- `AGENTS.md`가 schema 역할을 하고, `wiki/index.md`와 `wiki/log.md`가 탐색/추적의 기준점이 된다.

## Key Points
- `raw/`는 입력 레이어다.
- `wiki/sources/`, `wiki/topics/`, `wiki/entities/`, `wiki/decisions/`는 해석 레이어다.
- `wiki/index.md`는 content catalog다.
- `wiki/log.md`는 append-only timeline이다.

## Details
- source 페이지는 개별 원문을 요약한다.
- topic 페이지는 여러 source를 엮어 개념을 정리한다.
- entity 페이지는 사람, 조직, 제품을 추적한다.
- decision 페이지는 운영과 아키텍처의 선택을 기록한다.
- lint는 누락된 링크, 고아 페이지, 오래된 결론을 찾는 유지보수 단계다.

## Sources
- [Karpathy LLM Wiki Gist](../sources/karpathy-llm-wiki-gist.md)
- [raw snapshot](../../raw/notes/2026-05-09__karpathy-llm-wiki-gist.md)
- [AGENTS.md](../../AGENTS.md)
- [wiki/index.md](../index.md)
- [wiki/log.md](../log.md)

## Conflicts / Open Questions
- `wiki/raw_index/`는 아직 의미를 부여하지 않았다. 필요하면 source manifest로 바꿀 수 있다.
