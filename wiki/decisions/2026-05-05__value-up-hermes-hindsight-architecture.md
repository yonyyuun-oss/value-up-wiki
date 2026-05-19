# Value-Up Hermes Hindsight 아키텍처

목적: 현재 Hermes / Hindsight / NATS 아키텍처, 그 형성 배경이 된 비용 제약, 그리고 계속 위에 쌓아도 된다고 판단하게 만든 검증 결과를 자세히 기록한다.

## 왜 바꿨나
- 운영자는 이미 Claude와 Codex 구독을 둘 다 내고 있었고, 처음에는 구독 auth를 재사용하는 구성을 검토했다.
- 지금의 운영 결론은 `openai-codex / gpt-5.4`로 통일하는 쪽이다.
- 이유는 Claude 경로가 extra usage 제약과 운영 마찰을 만들었고, Codex 경로가 더 단순했기 때문이다.

## 지금 시스템이 하는 일
- `Hermes CEO` 는 `provider=openai-codex` 와 `model=gpt-5.4` 으로 동작한다.
- `Hermes CTO` 는 `provider=openai-codex` 와 `model=gpt-5.4` 으로 동작한다.
- `Hermes Critic` 는 `provider=openai-codex` 와 `model=gpt-5.4` 으로 동작한다.
- `Hermes` 런타임 메모리는 `memory.provider = hindsight` 로 켜져 있다.
- Hindsight는 역할별 로컬 외부 데몬으로 설정되어 있다.
- Hindsight는 `openai-codex` 와 `gpt-5.4` 를 직접 써서 Codex auth를 재사용한다.
- NATS bridge는 이제 외부 Hermes wrapper만 호출하고, Hermes 출력을 합성하지 않는다.

## provider 지원에서 배운 점
- Hermes 자체는 이미 Anthropic을 직접 지원하므로, CEO 역할에는 proxy가 필요하지 않았다.
- Hindsight local embedded mode도 Anthropic과 Claude Code provider 경로를 지원한다.
- 추가 API가 없는 가장 짧은 경로는 OpenAI-compatible proxy가 아니었다.
- 직접 Codex 경로가 더 낫다.
  - 유지할 추가 서비스가 없다
  - 추가 API 과금이 없다
  - 추가 번역 계층이 없다

## embedded local mode를 유지하지 않은 이유
- Hindsight embedded mode는 Claude Code provider를 초기화할 수 있었지만, 이 환경에서는 embedded PostgreSQL을 root로 시작하려다 실패했다.
- 실패 모드는 다음과 같다.
  - `initdb: error: cannot be run as root`
- 그래서 현재 host/container 구성에서는 embedded mode가 너무 취약했다.
- 실용적인 해결은 Hindsight를 로컬 외부 데몬으로 돌리는 것이었다.

## 최종 Hindsight 형태
- 각 Hermes 역할 컨테이너에는 다음이 있다.
  - `memory.provider = hindsight`
  - `127.0.0.1:8899` 에 뜨는 로컬 외부 Hindsight 데몬
  - 컨테이너 내부에 설치된 Hindsight 패키지 의존성
- 각 데몬은 non-root `hermes` 사용자로 시작된다.
- 각 데몬은 다음 값을 쓴다.
  - `HINDSIGHT_API_LLM_PROVIDER=openai-codex`
  - `HINDSIGHT_API_LLM_MODEL=gpt-5.4`
- 이렇게 하면 Hindsight도 Codex 경로에서 `gpt-5.4`를 그대로 사용한다.

## 운영 세부
- 실제 데몬 시작 경로는 `hindsight-embed`의 프로파일 메커니즘을 따른다.
- 각 역할 컨테이너의 실행 홈은 `HOME=/tmp/hindsight-<role>` 이고, Codex 인증은 그 홈 아래의 `~/.codex/auth.json` 을 본다.
- 현재는 공유 Codex auth를 각 데몬 홈에 복사해서 `openai-codex` 초기화를 안정화한다.
- 프로파일은 `hindsight-embed profile create hermes --port 8899 --merge ...` 로 등록했고, 활성 포트는 `8899` 이다.
- 데몬 헬스 체크는 `GET /health` 로 확인하며, 정상일 때는 `{"status":"healthy","database":"connected"}` 를 반환한다.
- 각 역할 컨테이너의 `hermes config show` 는 메인 모델과 보조 모델이 모두 `openai-codex / gpt-5.4` 임을 보여준다.

## 역할 라우팅
- `CEO`:
  - main model: `gpt-5.4`
  - auxiliary tasks는 OpenAI Codex에 고정
- `CTO`:
  - main model: `gpt-5.4`
- `Critic`:
  - main model: `gpt-5.4`

## 검증
- `hermes memory status` 에서 각 역할 컨테이너에 `hindsight` 가 active로 보인다.
- `hermes config show` 에서 각 역할 컨테이너의 메인 모델이 `openai-codex / gpt-5.4` 로 확인된다.
- `curl http://127.0.0.1:8899/health` 는 각 컨테이너에서 `healthy` 를 반환한다.
- `role_smoke.sh` 는 다음 조합으로 통과했다.
  - `CEO` = `openai-codex / gpt-5.4`
  - `CTO` = `openai-codex / gpt-5.4`
  - `Critic` = `openai-codex / gpt-5.4`
- `flow_value_up_smoke.sh` 도 통과했고 다음 이벤트를 발행한다.
  - `company.value-up.paperclip.issue.created`
  - `company.value-up.hermes.agent.run.started`
  - `company.value-up.hermes.agent.run.completed`
  - `company.value-up.audit.event.recorded`
- worker는 bridge ledger로 중복 issue 이벤트도 건너뛴다.

## 실용적 가치
- 현재 구성은 새 API meter를 추가하지 않고 이미 있는 Codex auth를 재사용한다.
- 라우팅은 한 줄로 요약된다.
  - CEO, CTO, Critic, Hindsight 모두 `openai-codex / gpt-5.4`
- 운영자가 다시 봐도, 역할별 차이는 모델보다 책임과 핸드오프 형식에 남아 있다.
- Hermes 전용 워크플로를 위에 계속 쌓아도 될 정도로 현재 구조는 단순하다.

## 남은 위험
- Hindsight 데몬은 여전히 역할별 런타임 의존성이어서 startup drift를 지켜봐야 한다.
- 데몬 홈 경로와 Codex auth 복사 경로가 어긋나면 `openai-codex` 초기화가 다시 실패할 수 있다.
- 역할별 prompt와 workflow 튜닝은 아직 끝나지 않았다.
- 더 넓은 Hermes surface 통합은 아직 남아 있다.

## 본 저장소에서 바뀐 파일
- `~/value-up/hermes/bootstrap_hindsight.sh`
- `~/value-up/hermes/STATUS.md`
- `~/value-up/hermes/README.md`
- `~/value-up/infra/nats/SESSION_HANDOFF.md`

## 관련 위키 페이지
- [Hermes 역할 라우팅 및 진행 스냅샷](./2026-05-05__value-up-hermes-role-routing-and-progress-snapshot.md)
- [활동 로그](../log.md)
