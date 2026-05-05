# Value-Up Paperclip 인증된 HTTPS 접속

목적: Japan Paperclip 인스턴스를 plain HTTP public proxy에서 authenticated HTTPS 접속으로 옮긴 과정을 기록한다.

## 결정
- Paperclip backend는 `127.0.0.1:3100` loopback에 그대로 둔다.
- 호환성을 위해 예전 public HTTP proxy `http://5.104.85.67:3101` 도 유지한다.
- hostname 기반 HTTPS entrypoint `https://5.104.85.67.nip.io` 를 추가한다.
- Caddy를 Paperclip 앞에 두고 TLS를 종료한 뒤 `127.0.0.1:3100` 으로 reverse proxy 한다.
- Paperclip은 `authenticated` 모드로 두어 이제 UI가 anonymous access가 아니라 login을 요구한다.

## 상태
- `paperclip.service` 는 loopback에만 bind한다.
- `caddy.service` 는 `5.104.85.67.nip.io` 를 80/443에서 서비스하고 Paperclip으로 proxy한다.
- `paperclip-public-3101.service` 는 여전히 `5.104.85.67:3101 -> 127.0.0.1:3100` 으로 포워딩한다.
- `server.allowedHostnames` 에 `5.104.85.67.nip.io` 가 들어 있다.
- `auth.publicBaseUrl` 은 `https://5.104.85.67.nip.io` 로 설정돼 있다.
- 로그인된 human 계정 `yhs@kcp.co.kr` 는 `value-up` 의 `admin` 이고 `instance_admin` 도 갖고 있다.

## 위험
- HTTPS hostname은 `nip.io` DNS 매핑이 계속 살아 있어야 한다.
- 예전 HTTP public proxy가 아직 있어서, 사용자가 IP:3101 URL을 계속 쓰면 TLS를 우회할 수 있다.
- 인스턴스가 아직 완전히 claim되지 않았다면 board claim과 sign-in 흐름에 운영자 확인이 한 번 더 필요할 수 있다.

## 다음
- HTTPS가 안정되면 예전 HTTP proxy를 없앨지 정한다.
- 실제 browser에서 board claim과 user sign-in 흐름을 확인한다.
- public hostname이 또 바뀌면 hostname allowlist를 맞춘다.

## 완료 기준
- `https://5.104.85.67.nip.io` 가 laptop이나 phone browser에서 열린다.
- authenticated Paperclip 작업에는 login이 필요하다.
- HTTPS URL이 handoff 문서와 wiki log에 기록된다.
- 현재 operator account가 더 이상 `No company access` 를 보지 않는다.

## 관련
- [Paperclip 공개 접속 URL](./2026-05-06__value-up-paperclip-public-access-url.md)
- [Hermes 역할 정의 계약](./2026-05-06__value-up-hermes-role-definition-contract.md)
- [Hermes Hindsight 메모리 정책 기준선](./2026-05-06__value-up-hermes-hindsight-memory-policy-baseline.md)
- [Hermes Hindsight 검색 필터 기준선](./2026-05-06__value-up-hermes-hindsight-retrieval-filter-baseline.md)
- [Hermes Hindsight 보조 기능 고정 기준선](./2026-05-06__value-up-hermes-hindsight-auxiliary-pinning-baseline.md)
- [활동 로그](../log.md)
