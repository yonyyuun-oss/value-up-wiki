# Value-Up Hermes Hindsight 메모리 정책 기준선

목적: CEO / CTO / Critic 계약이 고정된 뒤 처음으로 적용한 역할별 Hindsight 정책을 기록한다.

## 결정
- Hindsight는 이제 하나의 generic prompt 대신 역할별 memory mission을 사용한다.
- `CEO`는 가장 넓은 메모리 정책을 받는다.
  - `recall_budget = high`
  - `recall_prefetch_method = reflect`
  - `auto_retain = true`
  - `tags = role:ceo`
- `CTO`는 구현 중심 정책을 받는다.
  - `recall_budget = mid`
  - `recall_prefetch_method = recall`
  - `auto_retain = true`
  - `tags = role:cto`
- `Critic`은 검토 중심 정책을 받는다.
  - `recall_budget = mid`
  - `recall_prefetch_method = recall`
  - `auto_retain = true`
  - `tags = role:critic`
- mission 분리는 다음과 같다.
  - `CEO` = durable decisions, tradeoffs, escalation outcomes, architecture constraints
  - `CTO` = code structure, patch plans, reproductions, verified technical constraints
  - `Critic` = regressions, severity, evidence, acceptance criteria, risk patterns

## 상태
- 세 Hermes 역할 컨테이너는 이제 `/opt/data/config.yaml` 에 이 설정을 담고 있다.
- role tag는 identity tag일 뿐이며 content classification에는 쓰지 않는다.
- 역할 bank가 충분히 넓게 유용하도록 `recall_tags` 는 지금은 일부러 비워 두었다.
- `session_search`, compression, 기타 보조 동작은 아직 대부분 기존 Hermes 모델 라우팅의 영향을 받는다.

## 위험
- 실제 메모리 동작이 로그로 보이면 정책을 더 다듬어야 할 수도 있다.
- `auto_retain = true` 는 수집량을 늘리므로 mission이 엄격해야 한다.
- 태깅은 아직 최소 수준이라, bank가 너무 많은 무관 증거를 섞기 시작하면 나중에 더 엄격한 retrieval filter가 필요할 수 있다.

## 다음
- 새 mission이 유용한 retained memory를 만드는지 관찰한다.
- `recall_tags` 나 bank별 retrieval filter가 필요한지 결정한다.
- 그다음 Hindsight compression, search, retention 우선순위 조정으로 이어간다.

## 완료 기준
- 런타임이 역할마다 맞는 종류의 메모리를 obvious noise 없이 유지한다.
- 이후 역할 프롬프트가 role-appropriate context를 Hindsight에 기대어 꺼낼 수 있다.
- 구현 디테일을 언급하지 않아도 역할별 정책을 한 문장으로 설명할 수 있다.

## 관련
- [Hermes 역할 정의 계약](./2026-05-06__value-up-hermes-role-definition-contract.md)
- [Hermes Hindsight 아키텍처](./2026-05-05__value-up-hermes-hindsight-architecture.md)
- [활동 로그](../log.md)
