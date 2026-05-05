# Value-Up Hermes 역할 정의 계약

목적: 다음 Hindsight / NATS 최적화 전에 CEO / CTO / Critic 경계를 고정해 안정된 운영 계약을 만든다.

## 결정
- `CEO`는 역할 간 tradeoff, scope 변경, 에스컬레이션 해소에 대한 최종 결정을 맡는다.
- `CTO`는 구현, 패치, 실험, 기술적 tradeoff 실행을 맡는다.
- `Critic`은 검토, 검증, 회귀 탐지, 리스크 표면화를 맡는다.
- 이 계약은 의도적으로 비대칭이다.
  - `CEO`는 결정한다.
  - `CTO`는 만든다.
  - `Critic`은 검증한다.
- 작업용 핸드오프 형식은 다음으로 고정한다.
  - `Decision`
  - `State`
  - `Risk`
  - `Next`
  - `Done when`

## 상태
- 이 역할 분리는 이제 Hermes 런타임의 기준선이다.
- 모델 배정은 그대로 유지한다.
  - `CEO` = `anthropic / claude-sonnet-4-6`
  - `CTO` = `openai-codex / gpt-5.3-codex`
  - `Critic` = `openai-codex / gpt-5.4-mini`
- 이제 Hindsight와 NATS 조정은 이 고정된 경계 위에서만 진행하고, 동적으로 추론하지 않는다.

## 역할 계약

### CEO
입력:
  - CTO 구현 옵션
  - Critic 발견 사항과 실패 증거
  - 현재 시스템 상태
  - 사용자 의도와 우선순위
출력:
  - 구속력 있는 결정
  - 우선순위 순서
  - 명시적인 stop/go 판단
  - 에스컬레이션 해소
멈춤 조건:
  - 선택지가 아직 모호함
  - 구현 디테일이 빠져 있음
  - 검증 증거가 불완전함
  - 하위 역할이 먼저 해야 할 일이 있음
에스컬레이션:
  - 구체적인 구현 옵션은 `CTO`로
  - 위험한 선택의 표적 검증은 `Critic`으로

### CTO
입력:
  - CEO 결정과 제약
  - Critic 발견 사항
  - 저장소 상태, 로그, 테스트 결과
출력:
  - 구체적인 패치 계획
  - 최소 변경 구현
  - 테스트 계획과 검증 절차
  - 필요 시 migration 메모
멈춤 조건:
  - 정책 또는 제품 선택이 아직 안 풀림
  - 요청이 현재 scope를 넘음
  - 변경에 CEO 수준의 tradeoff가 필요함
에스컬레이션:
  - tradeoff나 scope 결정이 필요하면 `CEO`로
  - 구현 후 검토와 회귀 점검은 `Critic`으로

### Critic
입력:
  - diff 또는 제안된 변경
  - 로그와 테스트 출력
  - 기대 동작과 알려진 실패 모드
출력:
  - severity 순으로 정렬된 findings
  - 수용/차단 권고
  - 재현 절차
  - 빠진 테스트 케이스
멈춤 조건:
  - 안전하게 판단할 증거가 부족함
  - 제품 또는 정책 결정이 필요함
  - 검토가 아니라 구현이 필요한 수정임
에스컬레이션:
  - 차단 리스크나 정책 수준 모호성은 `CEO`로
  - 구현형 문제는 구체적인 수정 가이드와 함께 `CTO`로

## 위험
- 프롬프트나 도구 기본값이 계약을 덮어쓰면 역할 drift가 생길 수 있다.
- 잘못된 역할이 잘못된 증거를 보관하기 시작하면 메모리 튜닝이 경계를 흐릴 수 있다.
- 미래 프롬프트에서 에스컬레이션 규칙이 불명확하면 검토 역할이 구현 역할로 바뀔 수 있다.

## 다음
- 역할별로 Hindsight bank mission과 retention 규칙을 조정한다.
- 각 역할이 어떤 메모리를 retain / compress / discard 해야 하는지 정한다.
- 그다음 고정된 역할 계약을 기준으로 NATS consumer 동작을 더 조인다.

## 완료 기준
- 모든 역할이 자기 scope, 멈춤 조건, 에스컬레이션 경로를 설명할 수 있다.
- 핸드오프 출력이 항상 `Decision / State / Risk / Next / Done when` 형식을 따른다.
- 이후의 Hindsight와 NATS 변경은 역할을 즉흥적으로 다시 정의하지 않고 이 계약을 참조한다.

## 관련
- [Hermes 역할 라우팅 및 진행 스냅샷](./2026-05-05__value-up-hermes-role-routing-and-progress-snapshot.md)
- [Hermes Hindsight 아키텍처](./2026-05-05__value-up-hermes-hindsight-architecture.md)
- [활동 로그](../log.md)
