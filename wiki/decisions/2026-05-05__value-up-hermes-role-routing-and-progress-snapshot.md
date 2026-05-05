# Value-Up Hermes 역할 라우팅 및 진행 스냅샷

목적: 현재 Hermes 역할 라우팅, NATS worker provider 수정, 그리고 Hindsight Claude Code 메모리 데몬 경로를 기록한다.

## 요약
- `CEO` 는 이제 `anthropic / claude-sonnet-4-6` 으로 설정돼 있다.
- `CTO` 는 여전히 `openai-codex / gpt-5.3-codex` 이다.
- `Critic` 는 여전히 `openai-codex / gpt-5.4-mini` 이다.
- NATS Hermes worker가 `openai-codex` 를 기본값으로 쓰고 있었는데, 이것이 CEO 컨테이너 설정을 덮어써서 `claude-sonnet-4-6` 400 에러를 만들었다.
- CEO 보조 작업도 이제 Anthropic에 고정되어 smoke 경로에서 Codex fallback이 사라졌다.

## 핵심 포인트
- 가장 단순하고 안정적인 행동은 각 Hermes 컨테이너가 자기 `model.provider` 와 `model.model` 을 직접 갖게 두는 것이다.
- bridge는 명시적 override가 필요할 때만 `--provider` / `--model` 을 넘겨야 한다.
- Hindsight는 이제 역할별 로컬 외부 데몬으로 돌고, `claude-code / claude-sonnet-4-6` 을 직접 써서 별도 proxy 없이 Claude subscription auth를 재사용한다.
- 당장은 Hermes routing을 container-local로 두고 worker-side 기본 provider를 쓰지 않는 것이 임시방편이다.

## 세부 사항
- worker 기본 provider를 제거해서 `hermes_ceo` 가 이제 컨테이너에 설정된 provider/model을 직접 쓴다.
- 이렇게 하면 NATS bridge가 더 일반적이 되고, 모든 역할을 같은 provider로 강제로 보내는 실수를 피할 수 있다.
- 나중에 별도의 Hindsight proxy layer를 추가하더라도, 그것은 Claude가 아닌 provider 경로에만 쓸 것이다.
- CEO 컨테이너는 이제 compression, session search 같은 보조 작업도 Anthropic에 고정해서 Codex로 자동 fallback하지 않는다.

## 출처
- [Hermes worker](../../../value-up/infra/nats/hermes_worker_value_up.sh)
- [Hermes bootstrap](../../../value-up/hermes/bootstrap_hindsight.sh)
- [Hermes roles](../../../value-up/hermes/ROLES.md)
- [Hermes status](../../../value-up/hermes/STATUS.md)
- [Session handoff](../../../value-up/infra/nats/SESSION_HANDOFF.md)

## 충돌 / 열린 질문
- Hindsight가 Codex 기반 또는 OpenAI-compatible fallback도 지원해야 하는지는 아직 열려 있다.
- CEO Anthropic 경로와 NATS smoke는 보조 기능 고정 변경 이후 검증되었다.

## 관련 세부
- [Value-Up Hermes Hindsight 아키텍처](./2026-05-05__value-up-hermes-hindsight-architecture.md)
