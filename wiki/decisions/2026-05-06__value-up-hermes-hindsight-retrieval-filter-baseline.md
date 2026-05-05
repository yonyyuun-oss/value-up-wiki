# Value-Up Hermes Hindsight 검색 필터 기준선

목적: 역할별 memory mission을 적용한 뒤 처음으로 실제 retrieval filter를 고정한다.

## 결정
- Hindsight retrieval은 이제 예전 `tags` 필드에 기대지 않고 명시적인 role filter를 쓴다.
- 활성 filter 키는 다음이다.
  - `retain_tags`
  - `recall_tags`
  - `recall_tags_match`
  - `retain_source`
  - `retain_context`
- 호환성용 `tags` 키는 남겨 두되, 더 이상 주요 retrieval control로 보지 않는다.
- 역할 설정은 다음과 같다.
  - `CEO`: `retain_tags=role:ceo`, `recall_tags=role:ceo`, `recall_tags_match=all_strict`, `retain_source=hermes-ceo`, `retain_context=CEO decision workstream`
  - `CTO`: `retain_tags=role:cto`, `recall_tags=role:cto`, `recall_tags_match=all_strict`, `retain_source=hermes-cto`, `retain_context=CTO implementation workstream`
  - `Critic`: `retain_tags=role:critic`, `recall_tags=role:critic`, `recall_tags_match=all_strict`, `retain_source=hermes-critic`, `retain_context=Critic review workstream`

## 상태
- live 역할 컨테이너들은 이제 `/opt/data/config.yaml` 에 이 값들을 갖고 있다.
- bootstrap script는 미래 컨테이너에 대해서도 같은 filter set을 다시 생성한다.
- 덕분에 나중에 role contract를 다시 바꾸지 않고도 search와 compression을 조정할 수 있다.

## 위험
- `tags` 키가 호환성 때문에 남아 있어서, 문서가 어긋나면 나중에 보는 사람이 헷갈릴 수 있다.
- `CEO`는 여전히 prefetch에 `reflect`를 쓰므로, 이 쪽 role filtering은 explicit recall filter라기보다 retained-memory 정책에 가깝다.
- search/compression 우선순위는 아직 독립적으로 조정된 정책이 아니다.

## 다음
- 실제 retained memory에서 noise와 과도한 retention이 있는지 본다.
- 역할별로 `recall_max_tokens` 나 `retain_context` 를 조정할지 정한다.
- 그다음 명시적인 search/compression 우선순위 조정으로 넘어간다.

## 완료 기준
- 역할별 retained memory를 쉽게 필터링하고 이해할 수 있다.
- 예전 `tags` 키가 더 이상 어떤 문서나 스크립트에서도 source of truth가 아니다.
- 앞으로의 Hindsight 정책 변경은 활성 filter 키만 조정하면 된다.

## 관련
- [Hermes 역할 정의 계약](./2026-05-06__value-up-hermes-role-definition-contract.md)
- [Hermes Hindsight 메모리 정책 기준선](./2026-05-06__value-up-hermes-hindsight-memory-policy-baseline.md)
- [Hermes Hindsight 아키텍처](./2026-05-05__value-up-hermes-hindsight-architecture.md)
- [활동 로그](../log.md)
