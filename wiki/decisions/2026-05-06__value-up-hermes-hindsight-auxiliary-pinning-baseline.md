# Value-Up Hermes Hindsight 보조 기능 고정 기준선

목적: 역할 메모리 정책과 retrieval filter가 안정된 뒤 처음으로 명시적인 보조 작업 고정을 기록한다.

## 결정
- `session_search` 와 `compression` 은 이제 auto-detect에 맡기지 않고 역할별로 고정한다.
- `CEO`는 가장 넓은 auxiliary surface를 유지하고, 이 작업들에는 Anthropic을 쓴다.
- `CTO`는 `session_search` 와 `compression` 둘 다 `openai-codex` 역할 모델을 쓴다.
- `Critic`도 `session_search` 와 `compression` 둘 다 `openai-codex` 역할 모델을 쓴다.
- 이렇게 하면 recall 쪽 helper와 compression이 현재 작업과 같은 model family를 써서 routing drift를 줄일 수 있다.

## 상태
- bootstrap script가 이제 각 역할 컨테이너에 auxiliary task provider와 model을 써 넣는다.
- 역할 문서와 runtime status는 auxiliary search/compression을 더 이상 암묵적 fallback이 아니라 역할 계약의 일부로 본다.
- 현재 구성은 가장 자주 쓰는 auxiliary helper에 대해 auto-detect 의존을 멈춰야 한다.

## 위험
- 나중에 역할 model이 바뀌면 auxiliary pinning도 같이 옮겨야 하고, 그렇지 않으면 routing이 다시 drift한다.
- 더 넓은 auxiliary surface는 여전히 `CEO`가 가장 크므로, 나중에는 작업별 미세 조정이 필요할 수 있다.
- 이것은 helper가 더 좋아졌다는 뜻이 아니라, 이제 deterministic해졌다는 뜻이다.

## 다음
- 새 smoke 실행 뒤 로그에서 auxiliary auto-detect 경고가 나오는지 본다.
- 이 고정 이후에도 `retain_context` 나 `retain_every_n_turns` 를 다시 조정해야 하는지 정한다.
- 그다음 남은 search/compression 우선순위 조정으로 넘어간다.

## 완료 기준
- 중요한 역할에서는 `session_search` 와 `compression` 이 더 이상 auto-detect로 fallback하지 않는다.
- runtime log에 역할 모델이 deterministic하게 쓰이는 것이 보인다.
- auxiliary 동작을 best-effort 기본값이 아니라 역할 계약으로 설명할 수 있다.

## 관련
- [Hermes 역할 정의 계약](./2026-05-06__value-up-hermes-role-definition-contract.md)
- [Hermes Hindsight 메모리 정책 기준선](./2026-05-06__value-up-hermes-hindsight-memory-policy-baseline.md)
- [Hermes Hindsight 검색 필터 기준선](./2026-05-06__value-up-hermes-hindsight-retrieval-filter-baseline.md)
- [Hermes Hindsight 아키텍처](./2026-05-05__value-up-hermes-hindsight-architecture.md)
- [활동 로그](../log.md)
