목적: 위키 작업(수집, 조회, lint 통과)을 시간순으로 쌓는 append-only 로그.

## [2026-05-05] 초기화 | 저장소 골격
- 초기 `AGENTS.md` 스키마와 기본 위키 구조를 추가했다.

## [2026-05-05] 결정 | Hermes 역할 라우팅 스냅샷
- 현재 Hermes 모델 분할, NATS worker provider 덮어쓰기 수정, 그리고 남아 있던 Hindsight 라우팅 질문을 기록했다.

## [2026-05-05] 검증 | CEO 보조 기능 고정
- CEO 보조 작업을 Anthropic에 고정하고 Paperclip -> Hermes -> Audit smoke를 다시 돌려 성공했다.

## [2026-05-05] 검증 | Hindsight Claude Code 경로
- Hindsight 로컬 외부 런타임을 설치하고 provider를 활성화했으며, `claude-code / claude-sonnet-4-6` 이 Claude Code auth로 직접 동작함을 확인했다.

## [2026-05-05] 검증 | Hindsight 역할별 데몬
- 역할별 로컬 외부 Hindsight 데몬을 초기화하고, 각 Hermes 컨테이너에 `hindsight-all`을 설치했으며, CEO / CTO / Critic에 `memory.provider = hindsight`를 켰다.

## [2026-05-05] 결정 | Hermes Hindsight 아키텍처 세부
- Claude 구독 재사용 경로, embedded-mode PostgreSQL 실패, 로컬 외부 데몬 대체 경로, 그리고 최종 역할별 검증 상태를 담은 상세 아키텍처 메모를 추가했다.

## [2026-05-06] 결정 | Hermes 역할 정의 계약
- CEO / CTO / Critic 경계, 공통 핸드오프 형식, 에스컬레이션 규칙을 고정해 다음 Hindsight와 NATS 조정이 안정된 계약 위에서 진행되도록 했다.

## [2026-05-06] 결정 | Hermes Hindsight 메모리 정책 기준선
- CEO / CTO / Critic 각각에 역할별 Hindsight mission, recall budget, auto-retain 설정, identity tag를 적용했다.

## [2026-05-06] 결정 | Hermes Hindsight 검색 필터 기준선
- 활성 Hindsight 필터 키를 `retain_tags`, `recall_tags`, `recall_tags_match`, `retain_source`, `retain_context`로 바꾸고, `tags`는 호환성 용도로만 남겼다.

## [2026-05-06] 검증 | 검색 필터 기준선 반영 후 Hermes 역할/흐름 smoke
- Hindsight 검색 필터를 바꾼 뒤 `role_smoke.sh` 와 `flow_value_up_smoke.sh` 를 다시 실행했고 둘 다 통과했다.

## [2026-05-06] 검증 | Hindsight 정책 기준선 반영 후 Hermes 역할/흐름 smoke
- CEO / CTO / Critic에 대해 `role_smoke.sh` 를 다시 실행하고 `flow_value_up_smoke.sh` 도 다시 실행했으며, 메모리 정책 갱신 후 둘 다 통과했다.

## [2026-05-06] 결정 | Hermes Hindsight 보조 기능 고정 기준선
- 역할별로 `session_search` 와 `compression` 을 고정해서 보조 기능이 auto-detect 대신 역할 모델을 쓰도록 했다.

## [2026-05-06] 검증 | 보조 기능 고정 후 Hermes 역할/흐름 smoke
- 역할별 보조 기능을 고정한 뒤 `role_smoke.sh` 와 `flow_value_up_smoke.sh` 를 다시 실행했고 둘 다 통과했다.

## [2026-05-06] 결정 | Paperclip 공개 접속 URL
- 백엔드는 `127.0.0.1:3100` 에 그대로 두고, `http://5.104.85.67:3101` 로 별도 공개 프록시를 열었다.

## [2026-05-06] 결정 | Paperclip 인증된 HTTPS 접속
- 기존 HTTP 프록시는 호환성 때문에 유지하고, Caddy + 무료 Let’s Encrypt 인증서로 `https://5.104.85.67.nip.io` 를 추가했으며, Paperclip은 authenticated 모드로 전환했다.

## [2026-05-06] 검증 | Paperclip 회사 접근 수정
- 로그인된 human 계정 `yhs@kcp.co.kr`를 `value-up` 회사의 `admin`으로 추가하고 `instance_admin` 도 부여해서 `No company access` 상태를 해소했다.

## [2026-05-06] 결정 | Hermes 한국어 출력 정책
- Hermes가 사람에게 보여주는 출력은 기본적으로 한국어를 쓰고, 코드, 식별자, 파일명, API 이름, 인용 원문은 정확도를 위해 그대로 유지한다.

## [2026-05-06] decision | Hermes Korean output policy
- Defaulted Hermes-facing human output to Korean, while preserving exact code, identifiers, filenames, API names, and quoted source text when needed.

## [2026-05-09] decision | Value-Up Wiki LLM Wiki architecture
- Karpathy gist를 기준으로 `raw/`-`wiki/`-`schema` 3층 구조를 확정하고, source/topic/decision 페이지와 index/log 갱신 규칙을 문서화했다.

## [2026-05-09] ingest | Hermes README snapshot
- `value-up/hermes/README.md`를 `raw/notes/`에 스냅샷으로 보존하고, `wiki/sources/`, `wiki/topics/`, `wiki/entities/`를 연결해서 첫 ingest 루프를 돌렸다.

## [2026-05-09] correction | Removed cross-repo source misuse
- `value-up` 레포의 `hermes/README.md`를 잘못 source로 잡은 ingest를 정정하고, `value-up-wiki` 자체의 기준 source인 Karpathy LLM Wiki gist 스냅샷으로 재정렬했다.

## [2026-05-09] ingest | Karpathy LLM Wiki gist snapshot
- Karpathy gist를 `raw/notes/`에 스냅샷으로 저장하고, `wiki/sources/`와 `wiki/topics/`를 이 레포의 실제 기준 source에 맞게 연결했다.
