---
name: git
description: 변경 내용을 깔끔하고 직관성 높은 커밋 메시지로 구성하여 git add, commit, push까지 수행합니다. "git commit", "git push", "커밋해줘", "푸시해줘" 같은 요청에 사용하세요.
---

# Git Commit & Push

변경 사항을 커밋하고 원격 저장소에 푸시합니다.

## 절차

1. 병렬로 상태 파악:
   - `git status` — 추적되지 않은 파일 확인
   - `git diff` — staged/unstaged 변경 내용 확인
   - `git log --oneline -10` — 이 저장소의 커밋 메시지 스타일 확인
2. 변경 내용을 분석해 커밋 메시지를 작성한다.
   - "무엇을" 보다 "왜"에 집중한다.
   - 간결하게 1~2문장으로 작성한다.
   - 기존 커밋 메시지의 스타일(언어, 어투, 포맷)을 따른다.
   - 비밀 정보가 담긴 파일(.env, credentials 등)은 커밋하지 않고 사용자에게 알린다.
3. 관련 파일을 `git add`로 스테이징한다. (`git add -A`/`git add .` 대신 파일명을 명시)
4. 커밋 메시지를 HEREDOC으로 전달하여 커밋을 생성한다.
5. 커밋이 pre-commit hook으로 실패하면 원인을 수정하고 새로 커밋한다. (`--no-verify` 금지)
6. 커밋 성공 후 `git push`로 원격 저장소에 푸시한다.
   - 현재 브랜치가 원격 브랜치를 추적하지 않으면 `git push -u origin <branch>` 사용.
   - force push, `--no-verify` 등은 사용하지 않는다.
7. `git status`로 최종 상태를 확인하고 결과를 사용자에게 간단히 보고한다.

## 주의사항

- 변경 사항이 없으면(untracked/modified 없음) 커밋을 만들지 않는다.
- destructive 명령(`reset --hard`, `checkout --`, `clean -f` 등)은 사용하지 않는다.
- `git commit -i`, `git add -i`, `git rebase -i` 등 인터랙티브 명령은 사용하지 않는다.
