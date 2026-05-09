# Value-Up Wiki LLM Wiki Architecture

목적: `value-up-wiki`를 Karpathy의 LLM Wiki 패턴에 맞게 운영하기로 한 구조적 결정을 기록한다.

## Summary
- 이 저장소는 앞으로 `raw/`-`wiki/`-`schema` 3층 구조로 관리한다.
- `wiki/index.md`와 `wiki/log.md`는 필수 운영 파일로 유지한다.
- source/topic/entity/decision 페이지를 분리해서 누적 합성을 유지한다.

## Decision
- `raw/`는 불변 source layer로 둔다.
- `wiki/`는 LLM이 유지하는 합성 layer로 둔다.
- `AGENTS.md`는 운영 schema로 둔다.
- 새 source가 들어오면 source page, 관련 topic/entity page, index, log를 함께 갱신한다.
- 결정은 `wiki/decisions/`에 남기고, 개념 합성은 `wiki/topics/`에 남긴다.

## Rationale
- 한 페이지에 모든 것을 몰아넣으면 유지보수가 깨진다.
- source와 synthesis를 분리하면 사실과 해석이 섞이지 않는다.
- index/log를 강제하면 LLM이 다음 작업의 문맥을 쉽게 찾는다.

## Risks / Mitigations
- `raw/`가 비어 있으면 패턴이 문서로만 남을 수 있다.
  - 완화: 첫 ingest 때 실제 source를 `raw/`에 넣고 source page를 만든다.
- 초기에는 페이지 수가 적어서 index/log의 가치가 안 보일 수 있다.
  - 완화: 결정, topic, source를 동시에 쌓아 연결성을 만든다.

## Verification
- `AGENTS.md`에 raw/wiki/index/log/template 규칙이 존재한다.
- `wiki/index.md`가 catalog 역할을 한다.
- `wiki/log.md`가 append-only timeline 역할을 한다.
- `wiki/sources/`, `wiki/topics/`, `wiki/decisions/`에 실제 페이지가 존재한다.

## Sources
- [Karpathy LLM Wiki Gist](../sources/karpathy-llm-wiki-gist.md)
- [LLM Wiki Pattern](../topics/llm-wiki-pattern.md)
- [AGENTS.md](../../AGENTS.md)
- [wiki/index.md](../index.md)
- [wiki/log.md](../log.md)

## Conflicts / Open Questions
- `wiki/raw_index/`의 역할은 아직 확정하지 않았다.
- `raw/`의 최초 ingest 단위를 article/paper/note 중 어디에 둘지 아직 도메인별 합의가 필요하다.

