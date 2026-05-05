# Value-Up Hermes 한국어 출력 정책

목적: Hermes가 사람에게 보여주는 모든 출력의 기본 언어 규칙을 기록한다.

## 결정
- Hermes 출력, Paperclip 인접 agent prompt, 운영자용 요약은 기본적으로 모두 한국어로 쓴다.
- 정확한 원문이 필요한 code, identifier, filename, API 이름, 인용 원문은 그대로 유지한다.
- 다른 언어는 사용자가 명시적으로 요청할 때만 쓴다.

## 상태
- Paperclip -> Hermes bridge prompt에 한국어 출력 지침이 들어갔다.
- `ROLES.md` 에도 같은 운영 언어 규칙이 들어갔다.
- session handoff에도 같은 정책을 적어 두어 다음 작업에서도 일관되게 유지할 수 있게 했다.

## 위험
- 인용 원문은 사실성을 위해 원래 언어를 유지해야 할 수 있다.
- 외부 문서나 upstream tool output은 그대로 가져오면 한국어가 아닐 수 있다.

## 다음
- 이후 role이나 prompt를 바꿀 때도 이 규칙이 보이도록 유지한다.
- 새로운 agent surface가 생기면 rollout 전에 같은 언어 규칙을 먼저 넣는다.

## 완료 기준
- 새 Hermes-facing instruction과 summary가 기본적으로 한국어를 쓴다.
- 이 정책이 저장소 문서와 wiki handoff trail에 보인다.

## 관련
- [Hermes 역할 정의 계약](./2026-05-06__value-up-hermes-role-definition-contract.md)
- [Paperclip 인증된 HTTPS 접속](./2026-05-06__value-up-paperclip-authenticated-https-access.md)
- [활동 로그](../log.md)
