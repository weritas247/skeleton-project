# Vibe Coding Hybrid Branch Strategy

바이브코딩에 최적화된 3단계 브랜치 전략.
AI와 함께 빠르게 코딩하면서도 main을 안정적으로 유지한다.

## Branch Structure

```
main (production-ready)
 │
 ├── PR + CI 통과 필수
 │
stage (integration)
 │
 ├── PR (셀프머지 OK, diff 확인용)
 │
feature/* (바이브코딩 작업)
 │
 └── 자유 커밋 (흐름 유지)
```

## Branch Roles

| Branch | Purpose | Commit Style | Merge Method |
|---|---|---|---|
| `feature/*` | AI와 바이브코딩 | 자유 커밋, 빈번하게 | → stage PR (squash) |
| `stage` | 통합 검증, 중간 체크포인트 | feature PR 머지만 | → main PR (merge commit) |
| `main` | 배포 가능 상태 | stage PR 머지만 | - |

## Workflow

### 1. 작업 시작

```bash
git checkout stage
git pull origin stage
git checkout -b feature/cool-thing
```

### 2. 바이브코딩 (흐름 끊지 않기)

```bash
# AI 코드 생성 → 바로 커밋
git add -A && git commit -m "wip: try auth flow"
git add -A && git commit -m "wip: fix callback"
```

- 커밋 메시지 대충 OK (`wip`, `try this`)
- 자주 커밋, 망설이지 않기
- 실험 코드도 부담 없이 커밋

### 3. 단위 완성 → stage PR

```bash
git push -u origin feature/cool-thing
gh pr create --base stage --title "feat: auth flow" --body "..."
```

- diff 훑어보기 (AI가 뭘 넣었는지 확인)
- **squash merge**로 깔끔하게 압축
- feature 브랜치 삭제

### 4. stage 안정화 → main PR

```bash
gh pr create --base main --head stage --title "release: v1.2" --body "..."
```

- CI/테스트 통과 필수
- 여러 feature를 묶어서 올려도 OK
- **merge commit**으로 추적성 유지

## Why 3 Layers?

### 2단계 (feature → main)의 문제

- 바이브코딩 커밋이 지저분한데 바로 main에 들어감
- 롤백 단위가 커밋 레벨이라 복잡
- CI 실패 시 main이 불안정해짐

### 3단계의 이점

| Benefit | Description |
|---|---|
| **속도 유지** | feature에서 커밋 부담 zero |
| **품질 게이트** | stage→main에서 CI 검증 |
| **깔끔한 히스토리** | squash merge로 main은 의미 단위만 |
| **안전한 롤백** | stage에서 feature PR 단위 revert |
| **병렬 작업** | 여러 feature를 동시에 stage로 머지 후 통합 테스트 |

## Feature Branch Naming

```
feature/add-auth        # 기능 개발
feature/vibe-0409       # 날짜별 바이브 세션
feature/experiment-xyz  # 실험 (버릴 수도 있음)
fix/login-redirect      # 버그 수정
refactor/api-layer      # 리팩토링
```

## Decision Guide

### stage에서의 판단

```
동작한다 → main PR 생성
방향이 틀렸다 → feature PR revert, stage 깨끗하게 유지
실험이었다 → feature 브랜치만 삭제, stage에 안 머지
```

### 커밋 정리 타이밍

| Stage | Cleanup | Reason |
|---|---|---|
| feature | 안 함 | 속도 우선 |
| feature → stage | squash merge | 1커밋으로 압축 |
| stage → main | merge commit | feature 추적 가능 |

## Initial Setup

```bash
# main에서 stage 브랜치 생성
git checkout main
git checkout -b stage
git push -u origin stage

# GitHub에서 default branch protection 설정 (선택)
# - main: Require PR, Require CI pass
# - stage: Require PR (optional)
```

## Quick Reference

```bash
# 새 feature 시작
git checkout stage && git pull && git checkout -b feature/xxx

# 바이브코딩 중 빠른 커밋
git add -A && git commit -m "wip: ..."

# feature 완료 → stage PR
git push -u origin feature/xxx
gh pr create --base stage

# stage → main 릴리스
gh pr create --base main --head stage
```
