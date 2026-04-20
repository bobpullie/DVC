# DVC — Deterministic Verification Checklist

**Claude Code Skill Plugin** — 프로젝트별 결정론적 빌드 검증 프레임워크. 정적 분석 + 데이터 파일 검증 + 배포 정합성 점검을 하나의 checklist 시스템으로 통합.

**핵심 원칙:** 누적 버그를 **case로 일반화**하여 영구 회귀 방지 (Case Generalization).

## TEMS와의 구분

DVC ≠ TEMS TCL. 층위가 다르다:

| 구분 | DVC case | TEMS TCL |
|------|---------|---------|
| 대상 | **결정론적 빌드** 검증 | **LLM 행동** 교정 |
| 저장 | `src/checklist/cases.json` + `chk_*.py` | `memory/error_logs.db` (FTS5) |
| 식별자 | `DISPLAY_HUMANIZE_001` (도메인_동사_ID) | `#N` (정수) |
| 실행 | `python -m checklist.runner` (수동/cron) | preflight_hook (자동) |

## Install

Claude Code 글로벌 스킬 설치:

```bash
git clone https://github.com/bobpullie/DVC.git "$HOME/.claude/skills/dvc"
```

설치 후 `Skill` 도구에서 `dvc` 호출.

## Structure

```
dvc/
├── SKILL.md              # name: dvc, 진입점
├── ARCHITECTURE.md       # 시스템 구조 + 레이어 분리 근거
├── GUIDE.md              # case 작성 가이드 + 예시
├── init.py               # 프로젝트별 DVC 부트스트래핑
└── templates/            # case 템플릿 / runner 템플릿
```

## 프로젝트 적용

```bash
cd <PROJECT_ROOT>
python ~/.claude/skills/dvc/init.py
# → src/checklist/{cases.json,runner.py,chk_*.py} 스캐폴딩 생성
```

## Lifecycle

1. 버그 발견 → Case 작성 (cases.json)
2. 검증 함수 구현 (`chk_<name>.py`)
3. runner 등록
4. 회귀 발생 시 동일 case가 차단

## Related Plugins

- [TEMS](https://github.com/bobpullie/TEMS) — LLM 기억 시스템
- [SDC](https://github.com/bobpullie/SDC) — 서브에이전트 위임 계약
- [TWK](https://github.com/bobpullie/TWK) — LLM Wiki 3-Layer

## License

MIT — see [LICENSE](LICENSE).
