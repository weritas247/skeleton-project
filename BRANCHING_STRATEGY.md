# Git Branch Strategy

## Branches

- `main` — 배포 가능 상태. 직접 커밋 금지.
- `stage` — 통합 브랜치. feature PR만 받는다.
- `feature/*` — 작업 브랜치. stage에서 분기.

## Rules

- feature에서 자유롭게 커밋한다. 메시지 형식 제한 없음.
- feature → stage: **squash merge PR**. diff 확인 후 머지.
- stage → main: **merge commit PR**. CI 통과 필수.
- feature 머지 후 브랜치 삭제.

## Commands

```bash
# 새 작업
git checkout stage && git pull && git checkout -b feature/xxx

# 커밋
git add -A && git commit -m "wip: ..."

# feature → stage PR
git push -u origin feature/xxx
gh pr create --base stage

# stage → main PR
gh pr create --base main --head stage
```

## Setup

```bash
git checkout main
git checkout -b stage
git push -u origin stage
```
