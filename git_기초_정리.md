# Git 초기 설정 및 기본 명령어 정리

## 0. 핵심 개념: add / commit / push / pull

Git으로 작업할 때 파일이 이동하는 3단계 공간이 있습니다.

```
[작업 폴더] --add--> [스테이징 영역] --commit--> [로컬 저장소] --push--> [원격 저장소(GitHub)]
                                                        ^
                                                        |
                                                      pull
```

| 명령어 | 의미 | 비유 |
|---|---|---|
| **add** | 변경한 파일을 "다음 커밋에 포함할 목록"에 올리는 것. 아직 저장은 안 됨 | 택배 상자에 물건을 담는 것 |
| **commit** | 스테이징된 변경사항을 로컬 저장소에 하나의 "저장 지점(버전)"으로 기록 | 상자를 포장해서 창고(로컬)에 보관 |
| **push** | 로컬 저장소의 커밋들을 원격 저장소(GitHub)로 업로드 | 창고에 있던 상자를 실제로 GitHub 서버로 보냄 |
| **pull** | 원격 저장소(GitHub)의 최신 내용을 로컬로 받아오는 것 | GitHub 서버에 있는 상자를 내 컴퓨터로 받아옴 |

- add, commit은 **내 컴퓨터 안에서만** 일어나는 작업 (인터넷 연결 불필요)
- push, pull은 **GitHub 서버와 통신**하는 작업 (인터넷 연결 필요)

---

## 1. 계정 설정 (최초 1회, PC마다)

```bash
git config --global user.name "이름"
git config --global user.email "이메일주소"
```

기본 브랜치 이름을 `main`으로 통일하고 싶다면 (설정 안 하면 `git init` 시 `master`로 생성됨):

```bash
git config --global init.defaultBranch main
```

설정 확인:
```bash
git config --global --list
```

---

## 2. 로컬 폴더 ↔ GitHub 저장소 연결

### 이미 만든 로컬 폴더를 기존 GitHub repo와 연결할 때

```bash
git init                                          # 폴더를 git 저장소로 초기화
git remote add origin https://github.com/사용자명/저장소명.git
git remote -v                                     # 연결 확인
git pull origin main --allow-unrelated-histories  # 원격에 README 등 내용 있으면 먼저 받기
```

### 처음부터 GitHub repo를 통째로 받아올 때

```bash
git clone https://github.com/사용자명/저장소명.git
```

이 경우 로컬 폴더를 새로 만들 필요 없이, clone된 폴더를 VS Code에서 열면 됩니다.

---

## 3. 기본 작업 흐름: add → commit → push

```bash
git add .                    # 변경사항을 스테이징
git commit -m "커밋 메시지"    # 로컬에 기록 저장
git push origin main         # 원격 저장소(GitHub)에 반영
```

**주의:** `git init` 직후 브랜치 이름이 `master`인 경우가 많은데, GitHub 기본 브랜치는 `main`이라 이름을 맞춰야 push가 정상적으로 됩니다.

```bash
git branch -M main
git push -u origin main      # -u 옵션은 최초 1회만, 이후엔 origin/main과 자동 연결됨
```

이후부터는 `git push`만 입력해도 됩니다.

---

## 4. pull (원격 변경사항 가져오기)

```bash
git pull origin main
```

다른 곳에서 작업한 내용이나 GitHub 웹에서 직접 수정한 내용을 로컬로 받아올 때 사용합니다. 로컬과 원격의 커밋 히스토리가 완전히 다르면 (최초 연결 시) `--allow-unrelated-histories` 옵션이 필요할 수 있습니다.

---

## 5. 상태/이력 확인용 명령어

```bash
git status        # 현재 변경사항 상태 (어떤 파일이 추가/수정/스테이징됐는지)
git remote -v     # 연결된 원격 저장소 확인
git branch -a     # 로컬/원격 브랜치 목록 확인
git log --oneline # 커밋 이력 간단히 보기
```

---

## 6. 큰 데이터 파일은 gitignore 처리

CSV, pkl, zip 같은 대용량 데이터는 GitHub에 올리지 않는 것이 원칙입니다. (파일당 100MB 제한, repo 용량이 무거워지면 clone/pull 속도 저하)

`.gitignore` 파일 작성 예시:
```gitignore
data/
*.pkl
*.zip
```

이미 git이 추적 중이던 파일이면 gitignore를 추가해도 소용없으므로, 캐시에서 먼저 제거한 후 재커밋해야 합니다:

```bash
git rm -r --cached 폴더명
git add .gitignore
git commit -m "gitignore 적용"
```

> **gitignore 경로 주의사항**: `./`는 셸(shell)과 달리 gitignore에서 문자 그대로 인식되어 매칭이 안 됩니다. `폴더/파일` 또는 `/폴더/파일` 형태로 작성해야 합니다.

---

## 7. 전체 흐름 요약 (최초 세팅 시)

```bash
# 1) 계정 설정 (최초 1회)
git config --global user.name "이름"
git config --global user.email "이메일"

# 2) 로컬 폴더를 GitHub repo와 연결
git init
git remote add origin https://github.com/사용자명/저장소명.git
git pull origin main --allow-unrelated-histories

# 3) 작업 후 반영
git add .
git commit -m "작업 내용"
git branch -M main
git push -u origin main

# 4) 이후에는 반복 작업
git add .
git commit -m "메시지"
git push
```
