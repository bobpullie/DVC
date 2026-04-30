# DVC — Deterministic Verification Checklist

**Claude Code Skill Plugin** — 프로젝트별 결정론적 빌드 검증 프레임워크. 정적 분석 + 데이터 파일 검증 + 배포 정합성 점검을 하나의 checklist 시스템으로 통합.

**핵심 원칙:** 누적 버그를 **case로 일반화**하여 영구 회귀 방지 (Case Generalization).

---

## 5-Asset 4 원칙 (Independence + Local-Only + Separate-Repo + Universal Portability)

본 자산은 Triad Chord Studio 5-Asset 체계 (**TEMS / SDC / DVC / TWK / handover**) 의 한 축. 다음 4 게이트 원칙을 모두 만족해야 canonical GitHub 레포에 push 허용:

1. **Independence** — 5자산 상호 의존 0. DVC 는 TEMS / SDC / TWK / handover 미설치 환경에서도 self-contained 작동.
2. **Local-Only** — DVC case (`cases.json`) 와 검증기 (`chk_*.py`) 는 각 프로젝트 `src/checklist/` 로컬 한정. hub 디렉토리 금지.
3. **Separate-Repo** — 각 자산 별도 canonical 레포 보유. 한 PR 에 두 레포 묶지 않음.
4. **Universal Portability** — Windows/Linux/macOS, 임의 OS user, 임의 에이전트명 작동. 절대경로/특정 user-name/hub 의존 금지 — DVC case `TEMS_PATH_ORPHAN_001` 가 위반 정적 검출.

위반 발견 시 즉시 일반화 PR.

---

## TEMS와의 구분

DVC ≠ TEMS TCL. 층위가 다르다:

| 구분 | DVC case | TEMS TCL |
|------|---------|---------|
| 대상 | **결정론적 빌드** 검증 | **LLM 행동** 교정 |
| 저장 | `src/checklist/cases.json` + `chk_*.py` | `memory/error_logs.db` (FTS5) |
| 식별자 | `DISPLAY_HUMANIZE_001` (도메인_동사_ID) | `#N` (정수) |
| 실행 | `python -m checklist.runner` (수동/cron) | preflight_hook (자동) |

## Install

**Option A — 글로벌 스킬 (모든 프로젝트 공통):**

```bash
git clone https://github.com/bobpullie/DVC.git "$HOME/.claude/skills/dvc"
```

**Option B — 프로젝트 로컬 스킬 (특정 프로젝트만):**

```bash
cd <PROJECT_ROOT>
git clone https://github.com/bobpullie/DVC.git .claude/skills/dvc
```

설치 후 `Skill` 도구에서 `dvc` 호출.

## Updating

```bash
# Option A (global)
git -C "$HOME/.claude/skills/dvc" pull origin main

# Option B (project-local)
git -C "<PROJECT_ROOT>/.claude/skills/dvc" pull origin main
```

누구든 upstream에 push → 모든 에이전트가 `git pull` 로 업데이트 수신.

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
