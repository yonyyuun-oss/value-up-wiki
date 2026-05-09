# Raw Sources

목적: 이 저장소의 불변 소스 레이어를 정의한다.

## Summary
- `raw/`에는 외부에서 들어온 원문 자료를 둔다.
- 한 번 ingest된 소스는 덮어쓰지 않는다.
- 수정이 필요하면 원본을 고치지 말고 새 버전의 소스를 추가한다.

## Key Points
- `raw/`는 source of truth다.
- LLM은 `raw/`를 읽지만, 여기의 파일을 편집하지 않는다.
- 소스별로 읽기 쉬운 파일명을 사용한다.

## Details
- 권장 하위 폴더:
  - `raw/articles/`: 웹 글, 블로그, 장문 노트
  - `raw/papers/`: 논문, PDF, 리포트
  - `raw/notes/`: 사용자가 직접 넣은 메모, 발췌, 정리 전 텍스트
  - `raw/assets/`: 이미지, 스크린샷, 첨부 파일
- 새 소스가 들어오면 해당 파일은 그대로 보존하고, 해석과 요약은 `wiki/`에서 한다.
- 같은 소스의 후속 버전이 생기면 새 파일로 추가하고, 이전 파일은 남겨 둔다.

## Sources
- [AGENTS.md](../AGENTS.md)
- [wiki/index.md](../wiki/index.md)

