# Value-Up Hermes 역할 라우팅 및 진행 스냅샷

목적: 현재 Hermes 역할 라우팅, NATS worker provider 수정, 그리고 Hindsight Codex 메모리 데몬 경로를 기록한다.

## 요약
- `CEO`, `CTO`, `Critic` 모두 `openai-codex / gpt-5.4` 로 설정돼 있다.
- NATS Hermes worker가 `openai-codex` 를 기본값으로 쓰더라도, 각 컨테이너의 명시적 `model.provider` / `model.model` 설정이 우선한다.
- CEO 보조 작업도 Codex에 고정되어 smoke 경로에서 provider drift가 사라졌다.
- Hindsight 메모리 데몬도 `openai-codex / gpt-5.4` 로 맞춰져 있다.

## 핵심 포인트
- 가장 단순하고 안정적인 행동은 각 Hermes 컨테이너가 자기 `model.provider` 와 `model.model` 을 직접 갖게 두는 것이다.
- bridge는 명시적 override가 필요할 때만 `--provider` / `--model` 을 넘겨야 한다.
- Hindsight는 이제 역할별 로컬 외부 데몬으로 돌고, `openai-codex / gpt-5.4` 를 직접 써서 별도 proxy 없이 Codex auth를 재사용한다.
- Hermes routing을 container-local로 두고 worker-side 기본 provider를 쓰지 않는 것이 현재 운영 방식이다.

## 세부 사항
- worker 기본 provider를 제거해서 `hermes_ceo` 가 이제 컨테이너에 설정된 provider/model을 직접 쓴다.
- 이렇게 하면 NATS bridge가 더 일반적이 되고, 모든 역할을 같은 provider로 강제로 보내는 실수를 피할 수 있다.
- 나중에 별도의 Hindsight proxy layer를 추가하더라도, 그것은 Claude가 아닌 provider 경로에만 쓸 것이다.
- CEO 컨테이너는 이제 compression, session search 같은 보조 작업도 Codex에 고정해서 fallback하지 않는다.

## 출처
- [Hermes worker](../../../value-up/infra/nats/hermes_worker_value_up.sh)
- [Hermes bootstrap](../../../value-up/hermes/bootstrap_hindsight.sh)
- [Hermes roles](../../../value-up/hermes/ROLES.md)
- [Hermes status](../../../value-up/hermes/STATUS.md)
- [Session handoff](../../../value-up/infra/nats/SESSION_HANDOFF.md)

## 충돌 / 열린 질문
- Hindsight startup 경로와 auth 배치가 현재 운영 홈 규칙을 계속 따라가는지만 지켜보면 된다.
- 지금의 핵심은 provider 선택이 아니라 컨테이너별 런타임 고정이다.

## 관련 세부
- [Value-Up Hermes Hindsight 아키텍처](./2026-05-05__value-up-hermes-hindsight-architecture.md)
