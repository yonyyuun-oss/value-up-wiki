# Value-Up Paperclip 공개 접속 URL

목적: Japan Paperclip 인스턴스의 첫 외부 접속 경로를 기록해서 Mac과 phone 클라이언트가 UI에 직접 닿을 수 있게 한다.

## 결정
- Paperclip backend는 `127.0.0.1:3100` loopback에 그대로 둔다.
- 전용 `socat` service로 `5.104.85.67:3101` 에 public HTTP entrypoint를 연다.
- Hermes container가 app-side URL shape를 바꾸지 않고도 Paperclip에 계속 닿을 수 있도록 Docker bridge 경로는 `172.17.0.1:3101` 을 유지한다.

## 상태
- `paperclip.service` 는 여전히 canonical local runtime이다.
- `paperclip-public-3101.service` 가 이제 `5.104.85.67:3101 -> 127.0.0.1:3100` 으로 포워딩한다.
- firewall은 이제 public network에서 `3101/tcp` 를 허용한다.

## 위험
- 이것은 TLS가 아닌 plain HTTP다.
- public entrypoint는 proxy이므로, 로컬 Paperclip service가 `127.0.0.1:3100` 에서 건강하게 유지돼야 한다.
- 나중에 Paperclip local port가 다시 바뀌면 두 proxy service를 함께 고쳐야 한다.

## 다음
- 나중에 HTTPS를 추가할지, 아니면 plain HTTP entrypoint를 안정된 operator URL로 유지할지 정한다.
- 로컬 Paperclip port가 또 바뀌면 Docker bridge 경로도 맞춘다.

## 완료 기준
- `http://5.104.85.67:3101` 이 laptop이나 phone browser에서 열린다.
- Docker caller는 여전히 `172.17.0.1:3101` 을 통해 Paperclip에 닿는다.
- public URL이 handoff 문서와 wiki log에 기록된다.

## 관련
- [Hermes 역할 정의 계약](./2026-05-06__value-up-hermes-role-definition-contract.md)
- [Hermes Hindsight 메모리 정책 기준선](./2026-05-06__value-up-hermes-hindsight-memory-policy-baseline.md)
- [Hermes Hindsight 검색 필터 기준선](./2026-05-06__value-up-hermes-hindsight-retrieval-filter-baseline.md)
- [Hermes Hindsight 보조 기능 고정 기준선](./2026-05-06__value-up-hermes-hindsight-auxiliary-pinning-baseline.md)
- [활동 로그](../log.md)
